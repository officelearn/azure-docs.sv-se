---
title: Azure Application insikter Snapshot Debugger uppgradering för .NET-appar | Microsoft Docs
description: Så här uppgraderar du Snapshot Debugger till den senaste versionen på Azure App Services eller via NuGet-paket
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899841"
---
# <a name="upgrading-the-snapshot-debugger"></a>Uppgradera Snapshot Debugger

För att tillhandahålla bästa möjliga säkerhet för dina data, flyttas Microsoft från TLS 1,0 och TLS 1,1, som har visat sig vara sårbara för bestämda angripare. Om du använder en äldre version av plats tillägget måste en uppgradering fortsätta att fungera. Det här dokumentet beskriver de steg som krävs för att uppgradera din ögonblicks bilds fel sökare till den senaste versionen. Det finns två primära uppgraderings vägar beroende på om du har aktiverat Snapshot Debugger med ett plats tillägg eller om du använde en SDK-NuGet som har lagts till i ditt program. Både uppgraderings Sök vägar beskrivs nedan. 

## <a name="upgrading-the-site-extension"></a>Uppgradera webbplats tillägget

Om du har aktiverat fel sökning av ögonblicks bilder med hjälp av webbplats tillägget kan du enkelt uppgradera med hjälp av följande procedur:

1. Logga in på Azure Portal.
2. Navigera till din resurs som har Application Insights och ögonblicks bilds fel sökning aktive rad. För en webbapp går du till exempel till App Service resurs:

   ![Skärm bild av enskild App Service resurs med namnet DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. När du har navigerat till din resurs klickar du på Application Insights på bladet översikt:

   ![Skärm bild av tre knappar. Knappen centrera med namnet Application Insights har valts](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Ett nytt blad öppnas med de aktuella inställningarna. Om du inte vill ha möjlighet att ändra inställningarna kan du lämna dem som de är. Knappen **Använd** längst ned på bladet är inte aktive rad som standard och du måste växla en av inställningarna för att aktivera knappen. Du behöver inte ändra några faktiska inställningar, i stället för att ändra inställningen och sedan ändra tillbaka den direkt. Vi rekommenderar att du ställer in inställningen för profileraren och väljer **Använd**.

   ![Skärm bild av sidan Application Insights App Service konfiguration med knappen Använd markerad i rött](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. När du klickar på **Verkställ**uppmanas du att bekräfta ändringarna.

    > [!NOTE]
    > Platsen kommer att startas om som en del av uppgraderings processen.

   ![Skärm bild av App Service Använd övervaknings fråga. Text ruta visar meddelande: "Vi kommer nu att tillämpa ändringar i dina appdata och installera våra verktyg för att länka din Application Insights-resurs till webbappen. Då startas webbplatsen om. Vill du fortsätta? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Klicka på **Ja** för att tillämpa ändringarna. Under processen visas ett meddelande som visar att ändringarna tillämpas:

   ![Skärm bild av meddelandet tillämpa ändringar – uppdaterings tillägg som visas i det övre högra hörnet](./media/snapshot-debugger-upgrade/updating-extensions.png)

När åtgärden har slutförts visas ett meddelande om att **ändringar har tillämpats** .

   ![Skärm bild av meddelanden som talar om att ändringar används](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Platsen har nu uppgraderats och är redo att användas.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Uppgradera Snapshot Debugger med SDK/NuGet

Om programmet använder en version av `Microsoft.ApplicationInsights.SnapshotCollector` nedan version 1.3.1 måste den uppgraderas till en [nyare version](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) för att fortsätta att fungera.
