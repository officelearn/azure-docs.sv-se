---
title: Azure Application Insights Snapshot Debugger uppgradering för .NET-appar | Microsoft Docs
description: Uppgradera Snapshot Debugger till den senaste versionen på Azure App Services eller via Nuget-paket
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632631"
---
# <a name="upgrading-the-snapshot-debugger"></a>Uppgradera Snapshot Debugger

Microsoft är rör sig bort från TLS 1.0 och TLS 1.1 som har visat sig vara sårbara för angripare som definieras för att ge bästa möjliga säkerheten för dina data. Om du använder en äldre version av tillägget plats för detta krävs en uppgradering till fortsätta arbeta. Det här dokumentet beskriver de steg som krävs för att uppgradera ditt Snapshot debugger till den senaste versionen. Det finns två primära Uppgraderingsvägar beroende på om du har aktiverat Snapshot Debugger med hjälp av ett webbplatstillägg eller om du har använt ett SDK/Nuget som lagts till i ditt program. Båda uppgraderingsvägar som beskrivs nedan. 

## <a name="upgrading-the-site-extension"></a>Uppgradera site-tillägget

Om du har aktiverat Snapshot debugger med plats-tillägget är lätt att uppgradera på följande sätt:

1. Logga in på Azure Portal.
2. Gå till din resurs med Application Insights och Snapshot debugger aktiverat. Till exempel för en Webbapp, gå till App Service-resursen:

   ![Skärmbild av enskild App Service-resurs med namnet DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. När du har navigerat till resursen, klickar du på Application Insights i bladet översikt:

   ![Skärmbild av tre knappar. Center knapp med namnet Application Insights är markerat](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Ett nytt blad öppnas med de aktuella inställningarna. Om du inte vill passa på att ändra inställningarna, kan du lämna dem i befintligt skick. Den **tillämpa** knappen längst ned på bladet är inte aktiverad som standard och du måste aktivera en av inställningarna för att aktivera knappen. Du behöver ändra eventuella faktiska inställningar, i stället kan du ändra inställningen och sedan direkt ändra tillbaka den. Vi rekommenderar att växla mellan Profiler inställning och sedan välja **tillämpa**.

   ![Skärmbild av App Service konfiguration av Application Insights-sida med Verkställ markerat i rött](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. När du klickar på **tillämpa**, blir du ombedd att bekräfta ändringarna.

    > [!NOTE]
    > Webbplatsen kommer att startas om som en del av uppgraderingsprocessen.

   ![Skärmbild av App Service gäller övervakning prompten. Textrutan visar meddelande: ”Vi nu ändringar tillämpas inställningarna för din app och installera våra verktyg för att länka din Application Insights-resurs till webbappen. Webbplatsen startas om. Vill du fortsätta ”?](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Klicka på **Ja** att tillämpa ändringarna. Under processen visas ett meddelande som visar att ändringar tillämpas:

   ![Skärmbild av utför ändringar - uppdaterar tillägg meddelandet som visas i det övre högra hörnet](./media/snapshot-debugger-upgrade/updating-extensions.png)

När det har slutförts, en **”ändringarna tillämpas”** visas meddelandet.

   ![Skärmbild av meddelande om ändringarna tillämpas](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Platsen har uppgraderats och kan användas.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Uppgradera Snapshot Debugger med hjälp av SDK/Nuget

Om programmet använder en version av `Microsoft.ApplicationInsights.SnapshotCollector` nedan version 1.3.1 måste uppgraderas till en [nyare version](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) att fortsätta arbeta.
