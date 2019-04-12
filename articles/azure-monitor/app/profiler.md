---
title: Profilera live Azure App Service-appar med Application Insights | Microsoft Docs
description: Profilera live-appar på Azure App Service med Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493747"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilera live Azure App Service-appar med Application Insights

Du kan köra Profiler på ASP.NET och ASP.NET Core-appar som körs på Azure App Service med hjälp av grundläggande tjänstenivå eller högre. Aktivera Profiler på Linux är för närvarande bara möjligt via [den här metoden](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Aktivera Profiler för din app
Följ anvisningarna nedan om du vill aktivera Profiler för en app. Om du kör en annan typ av Azure-tjänst, följer du anvisningarna för att aktivera Profiler för andra plattformar som stöds:
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-program](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuella datorer](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler är förinstallerat som en del av App Services-runtime. Stegen nedan visar hur du aktiverar det för App Service. Följ dessa steg även om du tar med App Insights SDK i ditt program vid Byggtiden.

1. Aktivera inställningen ”alltid på” för app service. Du kan uppdatera inställningen i konfigurationssidan för App Service under allmänna inställningar.
1. Gå till den **Apptjänster** fönstret i Azure-portalen.
1. Gå till **Inställningar > Application Insights** fönstret.

   ![Aktivera App Insights på App Services-portalen](./media/profiler/AppInsights-AppServices.png)

1. Antingen följer du anvisningarna i fönstret för att skapa en ny resurs eller välj en befintlig App Insights-resurs för att övervaka din app. Kontrollera också att Profiler är **på**. Om Application Insights-resursen är i en annan prenumeration från din App Service, kan du inte använda den här sidan för att konfigurera Application Insights. Du kan fortfarande göra det manuellt om genom att skapa de nödvändiga app-inställningarna manuellt. [Nästa avsnitt innehåller anvisningar för att manuellt Aktivera Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Lägg till App Insights-webbplatstillägg][Enablement UI]

1. Profiler har nu aktiverats med hjälp av en Appinställning för App Services.

    ![App-inställning för Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Aktivera Profiler manuellt eller med Azure Resource Manager
Application Insights Profiler kan aktiveras genom att skapa appinställningar för Azure App Service. Sidan med alternativen som visas ovan skapar dessa app-inställningarna åt dig. Men du kan automatisera skapandet av de här inställningarna med hjälp av en mall eller på annat sätt. De här inställningarna fungerar även om Application Insights-resursen är i en annan prenumeration från Azure App Service.
Här följer inställningarna som krävs för att aktivera profiler:

|Programinställning    | Värde    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för Application Insights-resursen    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Du kan ange dessa värden med hjälp av [Azure Resource Manager-mallar](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).


## <a name="disable-profiler"></a>Inaktivera Profiler

Stoppa eller starta om Profiler för en enskild app-instansen under **Webbjobb**, gå till app-resursen. Ta bort Profiler genom att gå till **tillägg**.

![Inaktivera Profiler för ett webbjobb][disable-profiler-webjob]

Vi rekommenderar att du har Profiler som är aktiverad på alla dina appar för att identifiera några prestandaproblem så tidigt som möjligt.

Profilers filer kan tas bort när du använder WebDeploy för att distribuera ändringar i ditt webbprogram. Du kan förhindra att borttagningen genom att exkludera mappen App_Data tas bort under distributionen. 


## <a name="next-steps"></a>Nästa steg

* [Arbeta med Application Insights i Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
