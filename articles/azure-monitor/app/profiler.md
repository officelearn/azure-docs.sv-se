---
title: Profilera Live Azure App Service-appar med Application Insights | Microsoft Docs
description: Profilera Live-appar på Azure App Service med Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367714"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilera Live Azure App Service-appar med Application Insights

Du kan köra profiler på ASP.NET och ASP.NET Core appar som körs på Azure App Service med hjälp av Basic Service Tier eller högre. Det går för närvarande bara att aktivera profiler på Linux via [den här metoden](profiler-aspnetcore-linux.md).

## <a id="installation"></a>Aktivera profiler för din app
Följ anvisningarna nedan om du vill aktivera profiler för en app. Om du kör en annan typ av Azure-tjänst, finns här instruktioner för att aktivera profiler på andra plattformar som stöds:
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric program](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler är förinstallerat som en del av App Services Runtime. I stegen nedan visas hur du aktiverar det för App Service. Följ dessa steg även om du har inkluderat App Insights SDK i ditt program i bygge-tid.

1. Aktivera inställningen "Always On" för din app service. Du kan uppdatera inställningen på sidan konfiguration i App Service under allmänna inställningar.
1. Gå till rutan **app Services** i Azure Portal.
1. Gå till **inställningar > Application Insightss** fönstret.

   ![Aktivera App Insights på App Services-portalen](./media/profiler/AppInsights-AppServices.png)

1. Följ anvisningarna i fönstret för att skapa en ny resurs eller Välj en befintlig App Insights-resurs för att övervaka din app. Se också till att profileraren är **på**. Om din Application Insights-resurs finns i en annan prenumeration än App Service kan du inte använda den här sidan för att konfigurera Application Insights. Du kan fortfarande göra det manuellt, genom att skapa nödvändiga appinställningar manuellt. [Nästa avsnitt innehåller instruktioner för att manuellt aktivera profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Lägg till App Insights-webbplatstillägg][Enablement UI]

1. Profiler har nu aktiverats med hjälp av en Appinställning för App Services.

    ![App-inställning för Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Aktivera profiler manuellt eller med Azure Resource Manager
Application Insights Profiler kan aktive ras genom att skapa app-inställningar för din Azure App Service. Sidan med de alternativ som visas ovan skapar dessa inställningar för appen. Men du kan automatisera skapandet av de här inställningarna med hjälp av en mall eller på annat sätt. De här inställningarna kommer också att fungera om din Application Insights-resurs finns i en annan prenumeration än din Azure App Service.
Här är de inställningar som krävs för att aktivera profileraren:

|Programinställning    | Värde    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för din Application Insights-resurs    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Du kan ange dessa värden med hjälp av [Azure Resource Manager mallar](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Aktivera profiler för andra moln manuellt

Om du vill aktivera profileraren för andra moln kan du använda inställningarna för den här appen nedan.

|Programinställning    | AMERIKANSKA myndighets värden| Kina, moln |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Inaktivera Profiler

För att stoppa eller starta om profiler för en enskild app-instans går du till app-resursen under **webb jobb**. Om du vill ta bort profiler går du till **tillägg**.

![Inaktivera Profiler för ett webbjobb][disable-profiler-webjob]

Vi rekommenderar att du har profilerare aktiverat på alla dina appar för att upptäcka prestanda problem så tidigt som möjligt.

Profiler-filer kan tas bort när du använder WebDeploy för att distribuera ändringar i ditt webb program. Du kan förhindra borttagning genom att utesluta App_Data-mappen från att tas bort under distributionen. 


## <a name="next-steps"></a>Nästa steg

* [Arbeta med Application Insights i Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
