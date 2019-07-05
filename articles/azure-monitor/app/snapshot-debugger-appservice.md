---
title: Aktivera felsökning av ögonblicksbild för .NET-appar i Azure App Service | Microsoft Docs
description: Aktivera felsökning av ögonblicksbild för .NET-appar i Azure App Service
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: cf06c7c21d30fb107baee0d43b33b221c7da2773
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439851"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Aktivera felsökning av ögonblicksbild för .NET-appar i Azure App Service

Felsökning av ögonblicksbild fungerar för närvarande för ASP.NET och ASP.NET Core-appar som körs på Azure App Service på Windows service-planer.

## <a id="installation"></a> Aktivera felsökning av ögonblicksbild
Följ anvisningarna nedan om du vill aktivera felsökning av ögonblicksbild för en app. Om du kör en annan typ av Azure-tjänst, följer du anvisningarna för att aktivera Snapshot Debugger på andra plattformar som stöds:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure virtuella datorer och VM-skalningsuppsättningar](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Om du använder en förhandsversion av .NET Core, följer du anvisningarna för [aktiverar Snapshot Debugger för andra miljöer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) först för att inkludera den [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet paket med programmet och sedan slutföra resten av instruktionerna nedan. 

Application Insights Snapshot Debugger är förinstallerade som en del av App Services runtime, men du måste stänga in get-ögonblicksbilder för App Service-appen. När du har distribuerat en app, även om du har inkluderat Application Insights SDK i källkoden, följer du stegen nedan för att aktivera snapshot debugger.

1. Gå till den **Apptjänster** fönstret i Azure-portalen.
2. Gå till **Inställningar > Application Insights** fönstret.

   ![Aktivera App Insights på App Services-portalen](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Antingen följer du anvisningarna i fönstret för att skapa en ny resurs eller välj en befintlig App Insights-resurs för att övervaka din app. Kontrollera också att båda växlar för Snapshot Debugger är **på**.

   ![Lägg till App Insights-webbplatstillägg][Enablement UI]

4. Snapshot Debugger är nu aktiverat med hjälp av en Appinställning för App Services.

    ![App-inställning för Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Inaktivera Snapshot Debugger

Följ samma steg som för **aktiverar Snapshot Debugger**, men växlar båda växlar för Snapshot Debugger till **av**.
Vi rekommenderar att du har Snapshot Debugger aktiverad på alla dina appar för att underlätta diagnostik av undantagsfel.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program som kan utlösa ett undantag. Vänta 10 till 15 minuter för ögonblicksbilder som ska skickas till Application Insights-instans.
- Se [ögonblicksbilder](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) i Azure-portalen.
- Om du vill ha hjälp med felsökning av problem med Snapshot Debugger finns i [Snapshot Debugger felsökning](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

