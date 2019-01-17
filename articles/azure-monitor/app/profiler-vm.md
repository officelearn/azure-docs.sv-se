---
title: Profilera web apps som körs på en virtuell Azure-dator med hjälp av Application Insights Profiler | Microsoft Docs
description: Profilera web apps på en Azure-dator med hjälp av Application Insights Profiler.
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
ms.openlocfilehash: 3f720cdf369e7377f16bb2ea9cba7e898097cc29
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359790"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profil för web apps som körs på virtuella Azure-datorer eller en virtuell datorskalning in med hjälp av Application Insights Profiler

Du kan också distribuera Azure Application Insights Profiler på de här tjänsterna:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Distribuera Profiler på en virtuell dator eller en VM-skalningsuppsättning
Den här artikeln visar hur du hämtar Application Insights Profiler som körs på din Azure-dator (VM) eller Azure virtual machine scale Sets. Profiler har installerats med Azure Diagnostics-tillägget för virtuella datorer. Konfigurera tillägg för att köra Profiler och skapa Application Insights SDK i ditt program.

1. Lägg till Application Insights SDK till din [ASP.NET-program](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) eller vanliga [.NET-program](https://docs.microsoft.com/azure/application-insights/windows-services?toc=/azure/azure-monitor/toc.json).  
  Om du vill visa profiler för dina begäranden, måste du skicka begärandetelemetri till Application Insights.

1. Installera Azure-diagnostiktillägget på den virtuella datorn. Fullständig Resource Manager-mallexempel finns:  
    * [Virtuell dator](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Virtual machine scale Sets](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    Den viktiga delen är ApplicationInsightsProfilerSink i WadCfg. Om du vill att Azure Diagnostics aktivera Profiler att skicka data till din iKey måste du lägga till en annan mottagare till det här avsnittet.
    
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

   Tillämpa ändringarna som vanligtvis innebär att en fullständig mall-distribution eller en molnbaserad tjänst publicera via PowerShell-cmdletar eller Visual Studio.  

   Följande PowerShell-kommandon är en alternativ metod för befintliga virtuella datorer som rör vid endast Azure Diagnostics-tillägget. Lägg till de tidigare nämnda ProfilerSink konfig som returneras av kommandot Get-AzureRmVMDiagnosticsExtension och därefter skicka uppdaterade konfig till kommandot Set-AzureRmVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Om det avsedda programmet körs [IIS](https://www.microsoft.com/web/downloads/platform.aspx), aktivera den `IIS Http Tracing` Windows-funktionen.

   a. Upprätta fjärråtkomst till miljön och sedan använda den [lägga till Windows-funktioner]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) fönster. Eller kör följande kommando i PowerShell (som administratör):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Om upprätta fjärråtkomst har uppstått ett problem, kan du använda den [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) att köra följande kommando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Distribuera ditt program.

## <a name="can-profiler-run-on-on-premises-servers"></a>Profiler kan köras på lokala servrar?
Vi har inga planer på att stöd för Application Insights Profiler för lokala servrar.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program (till exempel starta en [tillgänglighetstestet](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability)). Vänta 10 till 15 minuter innan spårningarna starta som ska skickas till Application Insights-instans.
- Se [Profiler-spårningar](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
- Om du vill ha hjälp med felsökning av problem med Profiler finns i [Profiler felsökning](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
