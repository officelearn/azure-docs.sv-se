---
title: Aktivera Snapshot Debugger för .NET-appar i Azure App Service | Microsoft Docs
description: Aktivera Snapshot Debugger för .NET-appar i Azure App Service
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f6eb6376075337edd7656e4bc83b5b7fddde479
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899899"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Aktivera Snapshot Debugger för .NET-appar i Azure App Service

Snapshot Debugger fungerar för närvarande för ASP.NET och ASP.NET Core appar som körs på Azure App Service i Windows Service-planer.

## <a id="installation"></a>Aktivera Snapshot Debugger
Följ anvisningarna nedan om du vill aktivera Snapshot Debugger för en app. Om du kör en annan typ av Azure-tjänst, finns här instruktioner för att aktivera Snapshot Debugger på andra plattformar som stöds:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalnings uppsättningar för virtuella datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Om du använder en för hands version av .NET Core följer du anvisningarna för [att aktivera Snapshot debugger för andra miljöer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) för att först inkludera paketet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet med programmet, och Slutför resten av anvisningarna nedan. 

Application Insights Snapshot Debugger är förinstallerat som en del av App Services runtime, men du måste aktivera det för att skapa ögonblicks bilder för din App Service-app. När du har distribuerat en app, även om du har inkluderat Application Insights SDK i käll koden, följer du stegen nedan för att aktivera fel sökning av ögonblicks bild.

1. Gå till rutan **app Services** i Azure Portal.
2. Gå till **inställningar > Application Insightss** fönstret.

   ![Aktivera App Insights på App Services Portal](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Följ anvisningarna i fönstret för att skapa en ny resurs eller Välj en befintlig App Insights-resurs för att övervaka din app. Se också till att båda växlar för Snapshot Debugger finns **på**.

   ![Lägg till App Insights-webbplatsens tillägg][Enablement UI]

4. Snapshot Debugger har nu Aktiver ATS med en inställning för App Services app.

    ![App-inställning för Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Inaktivera Snapshot Debugger

Följ samma steg som för **aktivering av Snapshot debugger**, men växla båda växlar för Snapshot debugger till **av**.
Vi rekommenderar att du har Snapshot Debugger aktiverat i alla dina appar för att under lätta diagnostiken av program undantag.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program som kan utlösa ett undantag. Vänta sedan 10 till 15 minuter för ögonblicks bilder som ska skickas till Application Insights-instansen.
- Se [ögonblicks bilder](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) i Azure Portal.
- Hjälp med fel sökning Snapshot Debugger problem finns i [Snapshot debugger fel sökning](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

