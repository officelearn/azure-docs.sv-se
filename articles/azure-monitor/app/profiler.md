---
title: Profilera live Azure App Service-appar med Application Insights | Microsoft Docs
description: Profilera live-appar på Azure App Service med Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: daba72639e190bb255dd515237d25c20df433901
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075030"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilera live Azure App Service-appar med Application Insights

Profiler fungerar för närvarande för ASP.NET och ASP.NET Core-appar som körs på Azure App Service. Grundläggande tjänstenivån eller krävs senare för att använda Profiler. Aktivera Profiler på Linux är för närvarande bara möjligt via [den här metoden](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Aktivera Profiler för din app
Följ anvisningarna nedan om du vill aktivera Profiler för en app. Om du kör en annan typ av Azure-tjänst, följer du anvisningarna för att aktivera Profiler för andra plattformar som stöds:
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-program](../../azure-monitor/app/profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler är förinstallerade som en del av App Services runtime, men du måste stänga in get-profiler för App Service-appen. När du har distribuerat en app, även om du har inkluderat App Insights SDK i källkoden, följer du stegen nedan för att aktivera profiler.

1. Gå till den **Apptjänster** fönstret i Azure-portalen.
2. Gå till **Inställningar > Application Insights** fönstret.

   ![Aktivera App Insights på App Services-portalen](./media/profiler/AppInsights-AppServices.png)

3. Antingen följer du anvisningarna i fönstret för att skapa en ny resurs eller välj en befintlig App Insights-resurs för att övervaka din app. Kontrollera också att Profiler är **på**.

   ![Lägg till App Insights-webbplatstillägg][Enablement UI]

4. Profiler har nu aktiverats med hjälp av en Appinställning för App Services.

    ![App-inställning för Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Inaktivera Profiler

Stoppa eller starta om Profiler för en enskild app-instansen under **Webbjobb**, gå till app-resursen. Ta bort Profiler genom att gå till **tillägg**.

![Inaktivera Profiler för ett webbjobb][disable-profiler-webjob]

Vi rekommenderar att du har Profiler som är aktiverad på alla dina appar för att identifiera några prestandaproblem så tidigt som möjligt.

Om du använder WebDeploy för att distribuera ändringar i ditt webbprogram, kontrollerar du utesluter mappen App_Data tas bort under distributionen. Annars kan tas tillägget Profiler filer bort nästa gång du distribuerar webbappen till Azure.



## <a name="next-steps"></a>Nästa steg

* [Arbeta med Application Insights i Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
