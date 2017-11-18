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
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Identifiera hur kunder använder ditt program med Application Insights skorstenar

Förstå kundupplevelsen är av yttersta vikt för företaget. Om ditt program innefattar flera faser, kommer du behöver veta om de flesta kunder fortskrider genom hela processen, eller om de avslutar processen vid något tillfälle. Förlopp i en serie steg i ett webbprogram kallas ”Trattens”. Du kan använda Application Insights skorstenar och få insikter om dina användare och övervaka stegvisa konvertering priser. 

## <a name="create-your-funnel"></a>Skapa din tratten
Innan du skapar din Trattens måste du bestämma om du vill att besvara frågan. Exempelvis kan du ändå vill veta hur många användare som visar sidan, visa en kund-profil och skapa en biljett. Ägarna av Fabrikam Fiber företaget vill veta procentandelen kunder som att skapar en kund-biljett i det här exemplet.

Här är de steg som de vidtar för att skapa deras tratten.

1. Klicka på knappen Nytt i verktyget skorstenar.
1. Ange tidsintervall för ”senaste 90 dagarna” från den **tidsintervall** listrutan. Välj antingen ”min skorstenar” eller ”delat skorstenar”
1. Välj den **Index** händelse från den **steg 1** listrutan. 
1. Välj den **kunden** händelse från den **steg 2** listrutan.
1. Välj den **skapa** händelse från den **steg3** listrutan.
1. Lägg till ett namn i tratten och klicka på **spara**.

Följande bild visar data skorstenar verktyget genererar. Härifrån Fabrikam ser ägare att 54.3% av sina kunder som besökt sidan under de senaste 90 dagarna, skapas en kund-biljett. De kan också se att 2.7k för sina kunder kommer i index från startsidan, detta kan tyda på ett problem med uppdateringen.


![Skorstenar verktyget med data](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>Trattens funktioner
1. Om din app samplas visas en banderoll prover. När du klickar på banderollen öppnas en kontext fönstret instruerar inaktivera provtagning. 
2. Du kan exportera din Trattens till [Power BI](app-insights-export-power-bi.md).
3. Klicka på ett steg skaffa dig djupare insikter till höger. 
4. Historiska konvertering visar konverteringen under de senaste 90 dagarna. 
5. Förstå dina användare bättre genom att gå till verktyget användare från skorstenar. Varje steg får du granskat användare filter. 

## <a name="next-steps"></a>Nästa steg
  * [Översikt över användning](app-insights-usage-overview.md)
  * [Användare, sessioner och händelser](app-insights-usage-segmentation.md)
  * [Kvarhållning](app-insights-usage-retention.md)
  * [Arbetsböcker](app-insights-usage-workbooks.md)
  * [Lägg till användarkontext](app-insights-usage-send-user-context.md)
  * [Exportera till Power BI](app-insights-export-power-bi.md)

