---
title: Profilera web apps som körs på en virtuell Azure-dator med hjälp av Application Insights Profiler | Microsoft Docs
description: Profilera web apps på en Azure-dator med hjälp av Application Insights Profiler.
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
ms.openlocfilehash: 4cca65e2be44d2c846cd4034f0a9d7e8c7d9af28
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260056"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profil för web apps som körs på virtuella Azure-datorer eller en virtuell datorskalning in med hjälp av Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan också distribuera Azure Application Insights Profiler på de här tjänsterna:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Distribuera Profiler på en virtuell dator eller en VM-skalningsuppsättning
Den här artikeln visar hur du hämtar Application Insights Profiler som körs på din Azure-dator (VM) eller Azure virtual machine scale Sets. Profiler har installerats med Azure Diagnostics-tillägget för virtuella datorer. Konfigurera tillägg för att köra Profiler och skapa Application Insights SDK i ditt program.

1. Lägg till Application Insights SDK till din [ASP.NET-program](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) eller vanliga [.NET-program](windows-services.md?toc=/azure/azure-monitor/toc.json).  
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

   Följande PowerShell-kommandon är en alternativ metod för befintliga virtuella datorer som touch endast Azure Diagnostics-tillägget. Lägg till de tidigare nämnda ProfilerSink config som returneras av kommandot Get-AzVMDiagnosticsExtension. Sedan skicka uppdaterad konfig till kommandot Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
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

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Ställ in Profiler för mottagare med hjälp av Azure Resource Explorer
Vi har ännu inte möjlighet att ange mottagare för Application Insights Profiler från portalen. I stället för med hjälp av powershell som beskrivs ovan, du kan använda Azure Resource Explorer för att ange mottagaren. Men Observera att om du distribuerar den virtuella datorn igen, mottagaren kommer att gå förlorade. Du måste du uppdatera den konfiguration som du använder när du distribuerar den virtuella datorn för att bevara den här inställningen.

1. Kontrollera att Windows Azure Diagnostics-tillägget har installerats genom att visa de tillägg som installerats för den virtuella datorn.  

    ![Kontrollera om WAD tillägget har installerats][wadextension]

1. Hitta VM Diagnostics-tillägg för den virtuella datorn. Expandera din resursgrupp, Microsoft.Compute virtualMachines, namn på virtuell dator och tillägg.  

    ![Gå till WAD config i Azure Resource Explorer][azureresourceexplorer]

1. Lägg till Application Insights Profiler mottagare i noden SinksConfig under WadCfg. Om du inte redan har ett SinksConfig avsnitt, kan du behöva lägga till en. Glöm inte att ange den rätt Application Insights i dina inställningar. Du behöver växla Utforskare-läge till Läs/Skriv i det övre högra hörnet och tryck på den blå redigera-knappen.

    ![Lägg till Application Insights Profiler mottagare][resourceexplorersinksconfig]

1. När du är klar redigera config, tryck på ”Placera”. Om put lyckas visas en grön bock mitt på skärmen.

    ![Skicka put-begäran att tillämpa ändringar][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Profiler kan köras på lokala servrar?
Vi har inga planer på att stöd för Application Insights Profiler för lokala servrar.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program (till exempel starta en [tillgänglighetstestet](monitor-web-app-availability.md)). Vänta 10 till 15 minuter innan spårningarna starta som ska skickas till Application Insights-instans.
- Se [Profiler-spårningar](profiler-overview.md?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
- Om du vill ha hjälp med felsökning av problem med Profiler finns i [Profiler felsökning](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
