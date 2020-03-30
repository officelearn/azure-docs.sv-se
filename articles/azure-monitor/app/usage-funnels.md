---
title: Trattar för Azure Application Insights
description: Läs om hur du kan använda trattar för att upptäcka hur kunderna interagerar med ditt program.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 89440a6385bab5b917a866b686e8d2ba828c92e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671060"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Upptäck hur kunderna använder ditt program med Application Insights Trattar

Att förstå kundupplevelsen är av yttersta vikt för ditt företag. Om ditt program omfattar flera steg måste du veta om de flesta kunder går igenom hela processen, eller om de avslutar processen någon gång. Progressionen genom en serie steg i ett webbprogram kallas en *tratt*. Du kan använda Azure Application Insights-trattar för att få insikter om dina användare och övervaka stegvisa konverteringsfrekvenser. 

## <a name="create-your-funnel"></a>Skapa din tratt
Innan du skapar tratten bestämmer du dig för vilken fråga du vill besvara. Du kanske till exempel vill veta hur många användare som visar startsidan, visar en kundprofil och skapar en biljett. I det här exemplet vill ägarna till företaget Fabrikam Fiber veta hur stor procentandel av kunderna som har skapat en kundbiljett.

Här är de åtgärder de tar för att skapa sin tratt.

1. Välj **Nytt**i verktyget Programinsikter trattar .
1. Välj **Senaste 90 dagar**på den nedrullningsbara menyn **Tidsintervall** . Välj antingen **Mina trattar** eller **Delade trattar**.
1. Välj **Index**i listrutan **Steg 1** . 
1. Välj **Kund**i listan **Steg 2** .
1. Välj **Skapa**i listan **Steg 3** .
1. Lägg till ett namn i tratten och välj **Spara**.

Följande skärmbild visar ett exempel på vilken typ av data som verktyget Trattar genererar. Fabrikam-ägarna kan se att under de senaste 90 dagarna har 54,3 procent av deras kunder som besökt startsidan skapat en kundbiljett. De kan också se att 2.700 av deras kunder kom till indexet från hemsidan. Detta kan tyda på ett uppdateringsproblem.


![Skärmbild av trattar med data](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Funktioner för trattar
Den föregående skärmbilden innehåller fem markerade områden. Dessa är funktioner i Trattar. Följande lista förklarar mer om varje motsvarande område i skärmbilden:
1. Om du har ett prov på din app visas en samplingsbanderoll. Om du väljer banderollen öppnas ett sammanhangsfönster som förklarar hur du inaktiverar sampling. 
2. Du kan exportera tratten till [Power BI](../../azure-monitor/app/export-power-bi.md ).
3. Välj ett steg om du vill se mer information till höger. 
4. Det historiska konverteringsdiagrammet visar omräkningskurserna under de senaste 90 dagarna. 
5. Förstå användarna bättre genom att komma åt användarverktyget. Du kan använda filter i varje steg. 

## <a name="next-steps"></a>Nästa steg
  * [Översikt över användning](usage-overview.md)
  * [Användare, sessioner och händelser](usage-segmentation.md)
  * [Kvarhållning](usage-retention.md)
  * [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
  * [Lägga till användarkontext](usage-send-user-context.md)
  * [Exportera till Power BI](../../azure-monitor/app/export-power-bi.md )

