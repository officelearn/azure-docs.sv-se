---
title: Profil live Azure App Service-appar med Application Insights | Microsoft-dokument
description: Profil live apps på Azure App Service med Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275781"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profil live Azure App Service-appar med Application Insights

Du kan köra Profiler på ASP.NET och ASP.NET Core-appar som körs på Azure App Service med hjälp av grundläggande tjänstnivå eller högre. Aktivera Profiler på Linux är för närvarande endast möjligt via [denna metod](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a>Aktivera Profiler för din app
Om du vill aktivera Profiler för en app följer du instruktionerna nedan. Om du kör en annan typ av Azure-tjänst följer här instruktioner för hur du aktiverar Profiler på andra plattformar som stöds:
* [Molntjänster](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Program för serviceinfrastruktur](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuella datorer](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler är förinstallerad som en del av App Services-körningen. Stegen nedan visar hur du aktiverar den för din App Service. Följ dessa steg även om du har inkluderat App Insights SDK i ditt program vid byggtid.

1. Aktivera inställningen Alltid på för din apptjänst. Du kan uppdatera inställningen på sidan Konfiguration i apptjänsten under Allmänna inställningar.
1. Gå till **fönstret App Services** i Azure-portalen.
1. Navigera till **fönstret Inställningar > Programstatistik.**

   ![Aktivera appstatistik på App Services-portalen](./media/profiler/AppInsights-AppServices.png)

1. Följ antingen instruktionerna i fönstret för att skapa en ny resurs eller välj en befintlig App Insights-resurs för att övervaka din app. Kontrollera också att Profiler är **på**. Om din Application Insights-resurs finns i en annan prenumeration än din AppTjänst kan du inte använda den här sidan för att konfigurera Application Insights. Du kan fortfarande göra det manuellt men genom att skapa nödvändiga appinställningar manuellt. [Nästa avsnitt innehåller instruktioner för att manuellt aktivera Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Tillägg till webbplatstillägg för Lägg till appstatistik][Enablement UI]

1. Profiler är nu aktiverat med hjälp av appinställningen för App Services.

    ![Appinställning för Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Aktivera Profiler manuellt eller med Azure Resource Manager
Application Insights Profiler kan aktiveras genom att skapa appinställningar för din Azure App Service. Sidan med alternativen ovan skapar dessa appinställningar åt dig. Men du kan automatisera skapandet av dessa inställningar med hjälp av en mall eller på annat sätt. De här inställningarna fungerar också om din Application Insights-resurs finns i en annan prenumeration än din Azure App Service.
Här är de inställningar som behövs för att aktivera profiler:

|Programinställning    | Värde    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för din Application Insights-resurs    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Du kan ange dessa värden med [Azure Resource Manager-mallar](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Aktivera Profiler för andra moln manuellt

Om du vill aktivera profiler för andra moln kan du använda appinställningarna nedan.

|Programinställning    | Amerikanska regeringens värderingar| Kina Moln |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Inaktivera Profiler

Om du vill stoppa eller starta om Profiler för en enskild apps instans går du till appresursen under **Webbjobb.** Om du vill ta bort Profiler går du till **Tillägg**.

![Inaktivera Profiler för ett webbjobb][disable-profiler-webjob]

Vi rekommenderar att du har aktiverat Profiler i alla dina appar för att upptäcka eventuella prestandaproblem så tidigt som möjligt.

Profilers filer kan tas bort när webdeploy distribueras för att distribuera ändringar i webbprogrammet. Du kan förhindra att borttagningen utesluts genom att utesluta att App_Data-mappen tas bort under distributionen. 


## <a name="next-steps"></a>Nästa steg

* [Arbeta med Application Insights i Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
