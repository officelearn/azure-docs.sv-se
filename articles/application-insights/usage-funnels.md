---
title: Azure Application Insights skorstenar
description: "Lär dig hur du kan använda skorstenar för att identifiera hur kunder interagerar med programmet."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: bbb25af888f34737f6a61cf43890ff248c4cc4de
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Identifiera hur kunder använder ditt program med Application Insights skorstenar

Förstå customer experience är av yttersta vikt för företaget. Om ditt program innefattar flera faser, som du behöver veta om de flesta kunder fortskrider genom hela processen, eller om de avslutar processen vid något tillfälle. Förlopp i en serie steg i ett webbprogram kallas en *Trattens*. Du kan använda Azure Application Insights skorstenar och få insikter om dina användare och övervaka stegvisa konvertering priser. 

## <a name="create-your-funnel"></a>Skapa din tratten
Innan du skapar din Trattens besluta om du vill att besvara frågan. Exempelvis kan du ändå vill veta hur många användare som visar sidan, visa en kund-profil och skapa en biljett. Ägarna av Fabrikam Fiber företaget vill veta procentandelen kunder som att skapar en kund-biljett i det här exemplet.

Här är de steg som de vidtar för att skapa deras tratten.

1. Välj i verktyget Application Insights skorstenar **ny**.
1. Från den **tidsintervall** nedrullningsbara menyn och väljer **senaste 90 dagarna**. Välj antingen **min skorstenar** eller **delade skorstenar**.
1. Från den **steg 1** listrutan, Välj **Index**. 
1. Från den **steg 2** väljer **kunden**.
1. Från den **steg3** väljer **skapa**.
1. Lägg till ett namn i tratten och välj **spara**.

Följande skärmbild visar ett exempel på vilken typ av data skorstenar verktyget genererar. Fabrikam-ägare kan se det under de senaste 90 dagarna 54.3 procent av sina kunder som besökt sidan skapas en kund-biljett. De kan också se att 2,700 kunderna kommer i index från startsidan. Detta kan tyda på ett problem med uppdateringen.


![Skärmbild av skorstenar verktyget med data](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnels-features"></a>Skorstenar funktioner
Föregående skärmbild innehåller fem markerade områden. Dessa är skorstenar funktioner. I följande lista beskrivs mer om varje motsvarande område i skärmbilden:
1. Om din app samplas visas en banderoll prover. Om du markerar banderollen öppnas fönstret en kontext som förklarar hur du inaktiverar provtagning. 
2. Du kan exportera din Trattens till [Power BI](app-insights-export-power-bi.md).
3. Välj ett steg om du vill visa mer information till höger. 
4. Historiska konvertering diagrammet visar konvertering priser under de senaste 90 dagarna. 
5. Förstå dina användare bättre genom att öppna verktyget användare. Du kan använda filter i varje steg. 

## <a name="next-steps"></a>Nästa steg
  * [Översikt över användning](app-insights-usage-overview.md)
  * [Användare, sessioner och händelser](app-insights-usage-segmentation.md)
  * [Kvarhållning](app-insights-usage-retention.md)
  * [Arbetsböcker](app-insights-usage-workbooks.md)
  * [Lägg till användarkontext](app-insights-usage-send-user-context.md)
  * [Exportera till Power BI](app-insights-export-power-bi.md)

