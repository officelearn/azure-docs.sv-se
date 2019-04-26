---
title: Azure Application Insights Trattar
description: Lär dig hur du kan använda Trattar för att identifiera hur kunder interagerar med programmet.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/17/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 2cb7e15b701b53e74618c21bf219a355d495f985
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372931"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Upptäck hur kunder använder ditt program med Application Insights Trattar

Förstå kundupplevelsen är av stor betydelse för din verksamhet. Om ditt program omfattar flera faser, som du behöver veta om de flesta kunder fortskrider genom hela processen, eller om de avsluta processen vid en viss tidpunkt. Förlopp i en serie steg i ett webbprogram som kallas en *tratt*. Du kan använda Azure Application Insights Trattar insikter om dina användare och övervaka stegvisa konverteringstakten. 

## <a name="create-your-funnel"></a>Skapa din tratt
Innan du skapar din tratt besluta om du vill att besvara frågan. Exempelvis kan du ändå vill veta hur många användare visar sidan, visa en kundprofil och skapa en biljett. I det här exemplet ägarna av Fabrikam-Fiber-företag som vill veta procentandelen av kunder som har skapar ett ärende för kunden.

Här är de steg som de vidta för att skapa sina tratten.

1. Välj i Application Insights tratt-verktyget **New**.
1. Från den **tidsintervall** nedrullningsbara menyn och välj **senaste 90 dagarna**. Välj antingen **Mina trattar** eller **delade trattar**.
1. Från den **steg 1** listrutan, väljer **Index**. 
1. Från den **steg 2** väljer **kunden**.
1. Från den **steg3** väljer **skapa**.
1. Lägg till ett namn till tratten och välj **spara**.

Följande skärmbild visar ett exempel på vilken typ av data tratt-verktyget genererar. Fabrikam-ägare kan se att under de senaste 90 dagarna, 54.3 procent av sina kunder som har gjort Besök startsidan skapas en kund-biljett. De kan också se att 2,700 kundernas Kom till indexet på startsidan. Detta kan indikera ett problem med uppdateringen.


![Skärmbild av tratt-verktyget med data](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Trattar funktioner
Föregående skärmbild innehåller fem markerade områden. Det här är funktionerna i Trattar. I följande lista innehåller mer information om varje motsvarande område i skärmbilden:
1. Om din app samplas visas en banderoll för sampling. Om du väljer banderollen öppnas en kontextfönster förklarar hur du inaktiverar sampling. 
2. Du kan exportera din tratten [Power BI](../../azure-monitor/app/export-power-bi.md ).
3. Välj ett steg för att se mer information till höger. 
4. Historisk konvertering diagrammet visar valutaomvandling under de senaste 90 dagarna. 
5. Förstå användarna bättre genom att gå till användare-verktyget. Du kan använda filter i varje steg. 

## <a name="next-steps"></a>Nästa steg
  * [Användningsöversikten](usage-overview.md)
  * [Användare, sessioner och händelser](usage-segmentation.md)
  * [Kvarhållning](usage-retention.md)
  * [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
  * [Lägg till användarkontext](usage-send-user-context.md)
  * [Exportera till Power BI](../../azure-monitor/app/export-power-bi.md )

