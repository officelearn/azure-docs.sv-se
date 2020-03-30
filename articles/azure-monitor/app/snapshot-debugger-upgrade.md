---
title: Uppgradera ögonblicksbildfelsökaren för Azure Application Insights
description: Uppgradera Snapshot Debugger för .NET-appar till den senaste versionen på Azure App Services eller via Nuget-paket
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671400"
---
# <a name="upgrading-the-snapshot-debugger"></a>Uppgradera felsökaren för ögonblicksbilder

För att ge bästa möjliga säkerhet för dina data flyttar Microsoft från TLS 1.0 och TLS 1.1, som har visat sig vara sårbara för beslutsamma angripare. Om du använder en äldre version av webbplatstillägget krävs en uppgradering för att fortsätta fungera. I det här dokumentet beskrivs de steg som behövs för att uppgradera felsökaren för ögonblicksbild till den senaste versionen. Det finns två primära uppgraderingsvägar beroende på om du har aktiverat felsökaren för ögonblicksbilder med ett platstillägg eller om du har använt ett SDK/Nuget som lagts till i ditt program. Båda uppgraderingsvägarna beskrivs nedan. 

## <a name="upgrading-the-site-extension"></a>Uppgradera webbplatstillägget

> [!IMPORTANT]
> Äldre versioner av Application Insights använde ett privat webbplatstillägg som kallas _Application Insights-tillägget för Azure App Service_. Den aktuella application insights-upplevelsen aktiveras genom att ange appinställningar för att lysa upp ett förinstallerat platstillägg.
> För att undvika konflikter, vilket kan leda till att webbplatsen slutar fungera, är det viktigt att ta bort det privata webbplatstillägget först. Se steg 4 nedan.

Om du har aktiverat felsökaren för ögonblicksbilder med hjälp av platstillägget kan du uppgradera med följande procedur:

1. Logga in på Azure Portal.
2. Navigera till din resurs som har application insights och snapshot-felsökare aktiverade. För en webbapp navigerar du till apptjänstens resurs:

   ![Skärmbild av enskilda App Service-resurs med namnet DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. När du har navigerat till din resurs klickar du på bladet Tillägg och väntar på att listan med tillägg ska fyllas i:

   ![Skärmbild av tillägg för apptjänst som visar application insights-tillägget för Azure App Service installerat](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Om någon version av _Application Insights-tillägget för Azure App Service_ är installerad, välj det och klicka på Ta bort. Bekräfta **ja** om du vill ta bort tillägget och vänta tills borttagningen har slutförts innan du går vidare till nästa steg.

   ![Skärmbild av tillägg för apptjänst som visar application insights-tillägget för Azure App Service med knappen Ta bort markerad](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Gå till översiktsbladet för din resurs och klicka på Application Insights:

   ![Skärmbild av tre knappar. Centerknappen med namnet Application Insights är markerad](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Om det är första gången du visar bladet Application Insights för den här App-tjänsten uppmanas du att aktivera Application Insights. Välj **Aktivera Programstatistik**.
 
   ![Skärmbild av första gången för bladet Application Insights med knappen Aktivera programstatistik markerad](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Inställningarna för aktuella programstatistik visas. Om du inte vill ta tillfället i akt att ändra dina inställningar kan du lämna dem som de är. **Knappen Använd** på bladets undersida är inte aktiverad som standard och du måste växla en av inställningarna för att aktivera knappen. Du behöver inte ändra några faktiska inställningar, utan du kan ändra inställningen och sedan omedelbart ändra tillbaka den. Vi rekommenderar att du lägger på profilerinställningen och sedan väljer **Använd**.

   ![Skärmbild av sidan Konfiguration av Application Insights App Service med knappen Använd markerad i rött](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. När du klickar på **Verkställ**blir du ombedd att bekräfta ändringarna.

    > [!NOTE]
    > Platsen startas om som en del av uppgraderingsprocessen.

   ![Skärmbild av App Service:s övervakningsfråga. I textrutan visas meddelande: "Vi kommer nu att tillämpa ändringar i dina appinställningar och installera våra verktyg för att länka din Application Insights-resurs till webbappen. Detta kommer att starta om webbplatsen. Vill du fortsätta?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Klicka på **Ja** om du vill tillämpa ändringarna och vänta tills processen har slutförts.

Webbplatsen har nu uppgraderats och är klar att användas.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Uppgradera felsökaren för ögonblicksbilder med SDK/Nuget

Om programmet använder en `Microsoft.ApplicationInsights.SnapshotCollector` version av nedanstående version 1.3.1 måste det uppgraderas till en [nyare version](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) för att fortsätta arbeta.
