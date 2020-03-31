---
title: Profilwebbappar på en Virtuell Azure - Application Insights Profiler
description: Profilwebbappar på en Virtuell Azure-dator med hjälp av Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671587"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilwebbappar som körs på en virtuell Azure-dator eller en skala för virtuella datorer med hjälp av Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan också distribuera Azure Application Insights Profiler på dessa tjänster:
* [Azure App-tjänst](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Distribuera Profiler på en virtuell dator eller en skaluppsättning för virtuella datorer
Den här artikeln visar hur du får Application Insights Profiler körs på din Azure virtual machine (VM) eller Azure virtual machine scale set. Profiler installeras med Azure Diagnostics-tillägget för virtuella datorer. Konfigurera tillägget för att köra Profiler och skapa Program Insights SDK i ditt program.

1. Lägg till Programstatistik SDK i [ditt ASP.NET program](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

   Om du vill visa profiler för dina begäranden måste du skicka e-metri för begäran till Application Insights.

1. Installera Azure Diagnostics-tillägget på den virtuella datorn. Fullständiga exempel på Resource Manager-mall finns i:  
   * [Virtuell dator](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Skaluppsättning för virtuella datorer](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     Den viktigaste delen är ApplicationInsightsProfilerSink i WadCfg. Om du vill att Azure Diagnostics gör det möjligt för Profiler att skicka data till din iKey lägger du till ytterligare en diskbänk i det här avsnittet.
    
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

1. Distribuera den ändrade miljödistributionsdefinitionen.  

   Att tillämpa ändringarna innebär vanligtvis en fullständig malldistribution eller en molntjänstbaserad publicering via PowerShell-cmdletar eller Visual Studio.  

   Följande PowerShell-kommandon är en alternativ metod för befintliga virtuella datorer som bara berör Azure Diagnostics-tillägget. Lägg till de tidigare nämnda ProfilerSink i konfigurationen som returneras av kommandot Get-AzVMDiagnosticsExtension. Skicka sedan den uppdaterade konfigurationen till kommandot Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Om det avsedda programmet körs via [IIS](https://www.microsoft.com/web/downloads/platform.aspx)aktiverar du `IIS Http Tracing` Windows-funktionen.

   a. Upprätta fjärråtkomst till miljön och använd sedan fönstret [Lägg till Windows-funktioner.]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) Eller kör följande kommando i PowerShell (som administratör):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Om det är ett problem att upprätta fjärråtkomst kan du använda [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) för att köra följande kommando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Distribuera ditt program.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Ange profilerinkning med Azure Resource Explorer
Vi har ännu inte ett sätt att ställa in Application Insights Profiler-diskbänken från portalen. I stället för att använda powershell som beskrivs ovan kan du använda Azure Resource Explorer för att ange diskhon. Men observera, om du distribuerar den virtuella datorn igen, kommer diskhon att gå förlorad. Du måste uppdatera konfigurationen som du använder när du distribuerar den virtuella datorn för att bevara den här inställningen.

1. Kontrollera att tillägget Windows Azure Diagnostics installeras genom att visa de tillägg som är installerade för den virtuella datorn.  

    ![Kontrollera om WAD-tillägg är installerat][wadextension]

2. Hitta vm-diagnostiktillägget för den virtuella datorn. Gå [https://resources.azure.com](https://resources.azure.com)till . Expandera resursgruppen Microsoft.Compute virtualMachines, namn på virtuella datorer och tillägg.  

    ![Navigera till WAD-config i Azure Resource Explorer][azureresourceexplorer]

3. Lägg till programstatistikprofileräntan i sinksConfig-noden under WadCfg. Om du inte redan har ett SinksConfig-avsnitt kan du behöva lägga till ett. Var noga med att ange rätt Application Insights iKey i dina inställningar. Du måste växla explorers-läget till Läs/Skriv i det övre högra hörnet och tryck på den blå "Redigera"-knappen.

    ![Lägg till profilmottagare för programinsikter][resourceexplorersinksconfig]

4. När du är klar med redigeringen av konfigurationen trycker du på "Sätt". Om den sätta lyckas, kommer en grön kontroll visas i mitten av skärmen.

    ![Skicka begäran om att tillämpa ändringar][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Kan Profiler köras på lokala servrar?
Vi har inga planer på att stödja Application Insights Profiler för lokala servrar.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program (till exempel starta ett [tillgänglighetstest).](monitor-web-app-availability.md) Vänta sedan 10 till 15 minuter på att spårningar ska börja skickas till application insights-instansen.
- Se [Profiler-spårningar](profiler-overview.md?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
- Mer information om felsökning av Profiler-problem finns i [Felsökning av Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
