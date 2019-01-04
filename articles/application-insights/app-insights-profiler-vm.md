---
title: Profilera web apps som körs på en virtuell Azure-dator med Application Insights Profiler | Microsoft Docs
description: Profilera web apps på en virtuell Azure-dator med Application Insights Profiler.
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
ms.openlocfilehash: e1fe752a15fddffe8bd87954d3230e8e780fc819
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025070"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Profil för web apps som körs på en virtuell Azure-dator eller en VM-skalningsuppsättning med Application Insights Profiler
Du kan också distribuera Application Insights profiler på de här tjänsterna:
* [Azure App Service](../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Distribuera Profiler på en virtuell dator eller Scale Sets
Den här sidan vägleder dig igenom steg som behövs för att hämta Application Insights profiler som körs på dina virtuella Azure-datorer eller Azure-datorer ange. Application Insights Profiler har installerats med Windows Azure Diagnostics-tillägget för virtuella datorer. Tillägget måste konfigureras för körning av profiler och App Insights-SDK måste skapas i ditt program.

1. Lägg till application Insights SDK till din [ASP.Net-program](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) eller vanliga [.NET-program.](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) Du måste skicka begärandetelemetri till Application Insights finns profiler för dina begäranden.
1. Installera Windows Azure-diagnostiktillägget på den virtuella datorn. Fullständig Resource Manager-mallexempel finns:  
    * [Virtuell dator](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Virtual machine scale Sets](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    Den viktiga delen är ApplicationInsightsProfilerSink i WadCfg. Lägg till en annan mottagare i det här avsnittet som talar om WAD att aktivera profiler om du vill skicka data till din iKey.
    ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "ApplicationInsightsSink",
            "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
          },
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
          }
        ]
      },
    ```

1. Distribuera distributionsdefinitionen ändrade miljö.  

   Om du vill göra ändringar, det är oftast en fullständig mall-distribution eller en molntjänst baserad publicera via PowerShell-cmdletar eller Visual Studio.  

   Följande powershell-kommandon är en alternativ metod för befintliga virtuella datorer som rör vid endast Azure Diagnostics-tillägget. Du behöver bara lägga till ProfilerSink enligt vad som anges ovan i konfigurationen som returneras av kommandot Get-AzureRmVMDiagnosticsExtension. Sedan skicka uppdaterad konfig till kommandot Set-AzureRmVMDiagnosticsExcension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Om det avsedda programmet körs [IIS](https://www.microsoft.com/web/downloads/platform.aspx), aktivera den `IIS Http Tracing` Windows-funktionen.

   a. Upprätta fjärråtkomst till miljön och sedan använda den [lägga till Windows-funktioner]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) fönster och kör sedan följande kommando i PowerShell (som administratör):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Om upprätta fjärråtkomst har uppstått ett problem, kan du använda [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) att köra följande kommando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Distribuera ditt program.

## <a name="can-profiler-run-on-on-premises-servers"></a>Profiler kan köras på lokala servrar?
Vi har inga planer på att stöd för Application Insights Profiler för lokala servrar.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program (till exempel starta en [tillgänglighetstestet](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vänta 10 till 15 minuter innan spårningarna starta som ska skickas till Application Insights-instans.
- Se [Profiler-spårningar](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
- Få hjälp med felsökning av problem med profiler i [Profiler felsökning](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
