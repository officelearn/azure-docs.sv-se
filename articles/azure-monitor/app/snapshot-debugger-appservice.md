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
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784100"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Aktivera felsökning av ögonblicksbild för .NET-appar i Azure App Service

Felsökning av ögonblicksbild fungerar för närvarande för ASP.NET och ASP.NET Core-appar som körs på Azure App Service på Windows service-planer.

## <a id="installation"></a> Aktivera felsökning av ögonblicksbild
Följ anvisningarna nedan om du vill aktivera felsökning av ögonblicksbild för en app. Om du kör en annan typ av Azure-tjänst, följer du anvisningarna för att aktivera Snapshot Debugger på andra plattformar som stöds:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure virtuella datorer och VM-skalningsuppsättningar](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

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

* [Arbeta med Application Insights i Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

