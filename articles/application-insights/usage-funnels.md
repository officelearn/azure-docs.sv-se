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
ms.openlocfilehash: d7af89409cb908f98f86288a0d673ab287e3aaaa
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Identifiera hur kunder använder ditt program med Application Insights skorstenar

Förstå kundupplevelsen är av yttersta vikt för företaget. Om ditt program innefattar flera faser, kommer du behöver veta om de flesta kunder fortskrider genom hela processen, eller om de avslutar processen vid något tillfälle. Förlopp i en serie steg i ett webbprogram kallas ”Trattens”. Du kan använda Application Insights skorstenar och få insikter om dina användare och övervaka stegvisa konvertering priser. 

## <a name="get-started-with-the-funnels-blade"></a>Kom igång med bladet skorstenar
Det enklaste sättet att lära dig om skorstenar är att gå via ett exempel. Följande bilder visar stegen ägarna av en e-handel tar att lära dig hur kunderna samverkar med deras webbprogram.  

### <a name="create-your-funnel"></a>Skapa din tratten
Innan du skapar din Trattens måste du bestämma om du vill att besvara frågan. Du kanske vill veta hur många kunder visa startsidan-Klicka på en annons. I det här exemplet vill ägarna av företaget Fabrikam Fiber veta procentandelen kunder som gör ett inköp när du lägger till objekt till deras kundvagn under den senaste månaden.

Här är de steg som de vidtar för att skapa deras tratten.

1. Klicka på knappen Nytt i bladet skorstenar.
1. Ange tidsintervall för ”senaste månad” från den **tidsintervall** listrutan. 
1. Välj den **produktsidan** händelse från den **steg 1** listrutan. 
1. Välj den **Lägg till i kundvagn** händelse från den **steg 2** listrutan.
1. Välj den **Klicka på Köp** händelse från den **steg3** listrutan.
1. Lägg till ett namn i tratten och klicka på **spara**.

Följande bild visar data bladet skorstenar genererar. Härifrån Fabrikam ser ägare att 22.7% av sina kunder som lagts till ett objekt till deras kundvagn slutfört köpet under den senaste veckan. De kan också se att 1% av kunderna klickat på en annons innan besöka produktsidan och 20% av sina kunder loggas när du har slutfört sin inköp.


![Skorstenar bladet med data](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>Nästa steg
  * [Översikt över användning](app-insights-usage-overview.md)
  * [Användare, sessioner och händelser](app-insights-usage-segmentation.md)
  * [Kvarhållning](app-insights-usage-retention.md)
  * [Arbetsböcker](app-insights-usage-workbooks.md)
  * [Lägg till användarkontext](app-insights-usage-send-user-context.md)
