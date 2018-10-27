---
title: Profilera live-Azure-webbappar med Application Insights | Microsoft Docs
description: Profilen live-webbappar på Azure med Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 9aae08aa5906f341a890ac15e30d2863109d83a2
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140016"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilera live-Azure-webbappar med Application Insights

Profiler fungerar för närvarande för ASP.NET och ASP.NET Core web apps som körs på Web Apps. Grundläggande tjänstenivån eller krävs senare för att använda Profiler.

## <a id="installation"></a> Aktivera Profiler för Web Apps
Följ anvisningarna nedan om du vill aktivera Profiler för en webbapp. Om du kör en annan typ av Azure-tjänst, följer du anvisningarna för att aktivera Profiler för andra plattformar som stöds:
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-program](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


Application Insights Profiler har installerats med Application Insights-webbplatstillägg. Du måste installera platstillägget och konfigurera den för att få profiler för Azure Web Apps. När du har distribuerat en Webbapp, även om du har inkluderat App Insights SDK i källkoden, följer du stegen nedan för att aktivera profiler.

1. Gå till den **Apptjänster** fönstret i Azure-portalen.
1. Gå till **Inställningar > övervakning** fönstret.

   ![Aktivera App Insights på App Services-portalen](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Antingen följer du anvisningarna i fönstret för att skapa en ny resurs eller välj en befintlig App Insights-resurs för att övervaka ditt webbprogram. Acceptera alla standardalternativ. **Diagnostik på kodnivå** är aktiverad som standard och gör det möjligt för Profiler.

   ![Lägg till App Insights-webbplatstillägg][Enablement UI]

1. Profiler har installerats med App Insights-webbplatstillägg och aktiveras med hjälp av en Appinställning för App Services.

    ![App-inställning för Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Inaktivera Profiler

Stoppa eller starta om Profiler för en enskild webbapp-instansen under **Webbjobb**går du till Web Apps-resurs. Ta bort Profiler genom att gå till **tillägg**.

![Inaktivera Profiler för ett webbjobb][disable-profiler-webjob]

Vi rekommenderar att du har Profiler som är aktiverad på alla dina webbprogram för att identifiera några prestandaproblem så tidigt som möjligt.

Om du använder WebDeploy för att distribuera ändringar i ditt webbprogram, kontrollerar du utesluter mappen App_Data tas bort under distributionen. Annars kan tas tillägget Profiler filer bort nästa gång du distribuerar webbappen till Azure.



## <a name="next-steps"></a>Nästa steg

* [Arbeta med Application Insights i Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

