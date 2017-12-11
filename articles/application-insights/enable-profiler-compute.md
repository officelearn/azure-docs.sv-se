---
title: "Aktivera Application Insights Profiler för Azure Compute program | Microsoft Docs"
description: "Lär dig hur du ställer in Application Insights Profiler i ett program som körs i Azure Compute."
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: 57a4cb560825e0c05ac49df26ac12ee52da52c3c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-cloud-services"></a>Aktivera Application Insights Profiler för virtuella Azure-datorer, Service Fabric och Cloud Services

Den här artikeln visar hur du aktiverar Azure Application Insights Profiler på ett ASP.NET-program som finns i en Azure compute-resurs. 

Exemplen i den här artikeln har stöd för Azure virtuella datorer, virtuella datorer, Azure Service Fabric och Azure Cloud Services. Exemplen är beroende av mallar som har stöd för den [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) distributionsmodell.  


## <a name="overview"></a>Översikt

Följande bild visar hur Application Insights-profiler fungerar med Azure-resurser. Bilden används en virtuell Azure-dator som exempel.

  ![Översikt](./media/enable-profiler-compute/overview.png)

Om du vill aktivera profileraren helt måste du ändra konfigurationen på tre platser:

* Fönstret Application Insights-instans i Azure-portalen.
* Programmets källkod (exempelvis en ASP.NET-webbprogram).
* Miljö distribution definition källkoden (till exempel en VM-distribution mallen JSON-fil).


## <a name="set-up-the-application-insights-instance"></a>Konfigurera Application Insights-instans

Skapa eller gå till Application Insights-instans som du vill använda i Azure-portalen. Observera instrumentation instansnyckeln. Du använder nyckeln instrumentation i andra konfigurationssteg.

  ![Platsen för viktiga instrumentation](./media/enable-profiler-compute/CopyAIKey.png)

Den här instansen måste vara samma som ditt program. Den är konfigurerad för att skicka telemetridata till för varje begäran.
Profileraren resultat är tillgängliga i den här instansen.  

I Azure-portalen slutföra stegen som beskrivs i [aktivera profileraren](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) för att konfigurera Application Insights-instans för profileraren. Du behöver inte länka webbprogram till exempel i den här artikeln. Se bara till att profileraren är aktiverat i portalen.


## <a name="set-up-the-application-source-code"></a>Ställ in programmets källkod

Konfigurera programmet att skicka telemetridata till en Application Insights-instans på varje `Request` igen:  

1. Lägg till den [Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) till projektet. Kontrollera att NuGet-paketet versioner är följande:  
  - För ASP.NET-program: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 eller senare.
  - För program som ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 eller senare.
  - För andra .NET och .NET Core program (till exempel en tillståndslös Service Fabric-tjänst eller en arbetsroll i Cloud Services): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) eller [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 eller senare.  

2. Om ditt program är *inte* ett ASP.NET eller ASP.NET Core program (till exempel, om det är en arbetsroll för molntjänster eller Service Fabric tillståndslös API: er), krävs följande extra instrumentation inställningar:  

  1. Lägg till följande kod i början av livslängden för programmet:  

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    ...
    // Replace with your own Application Insights instrumentation key.
    TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
    ```
  Mer information om den här nyckeln konfigurationen globala instrumentation finns [används Service Fabric med Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2. För varje typ av kod som du vill instrumentera, lägga till en `StartOperation<RequestTelemetry>` **USING** instruktionen runt det, som i följande exempel:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    ...
    var client = new TelemetryClient();
    ...
    using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
    {
      // ... Code I want to profile.
    }
    ```

  Anropar `StartOperation<RequestTelemetry>` inom en annan `StartOperation<RequestTelemetry>` scope stöds inte. Du kan använda `StartOperation<DependencyTelemetry>` i den kapslade omfång i stället. Exempel:  

    ```csharp
    using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
    {
      try
      {
        ProductDetail details = new ProductDetail() { Id = productId };
        getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();

        // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
        using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
        {
            double price = await _priceDataBase.GetAsync(productId);
            if (IsTooCheap(price))
            {
                throw new PriceTooLowException(productId);
            }
            details.Price = price;
        }

        // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
        using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
        {
            details.Reviews = await _reviewDataBase.GetAsync(productId);
        }

        getDetailsOperation.Telemetry.Success = true;
        return details;
      }
      catch(Exception ex)
      {
        getDetailsOperation.Telemetry.Success = false;

        // This exception gets linked to the 'GetProductDetails' request telemetry.
        client.TrackException(ex);
        throw;
      }
    }
    ```


## <a name="set-up-the-environment-deployment-definition"></a>Ställ in distributionsdefinitionen miljö

Miljön där profileraren och kör ditt program kan vara en virtuell dator, en skaluppsättning för virtuell dator, ett Service Fabric-kluster eller en Cloud Services-instans.  

### <a name="virtual-machines-virtual-machine-scale-sets-or-service-fabric"></a>Skalningsuppsättningar i virtuella datorer, virtuella datorer eller Service Fabric

Fullständig exempel:  
  * [Virtuell dator](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Virtuella datorns skaluppsättning](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric-kluster](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Att se till att [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare används, det räcker att bekräfta att det distribuerade Operativsystemet är `Windows Server 2012 R2` eller senare.

2. Leta upp den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) tillägget på distributionsmallen för filen och Lägg sedan till följande `SinksConfig` avsnitt som ett underordnat element för `WadCfg`. Ersätt den `ApplicationInsightsProfiler` egenskapsvärde med din egen Application Insights instrumentation nyckel:  
  ```json
  "SinksConfig": {
    "Sink": [
      {
        "name": "MyApplicationInsightsProfilerSink",
        "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
      }
    ]
  }
  ```

  Information om att lägga till tillägget diagnostik i mallen för distribution finns [Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


### <a name="cloud-services"></a>Molntjänster

1. Att se till att [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare används, det räcker att bekräfta att ServiceConfiguration.\*. cscfg-filer har en `osFamily` värdet för **”5”** eller senare.

2. Leta upp den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) diagnostics.wadcfgx filen för ditt program:  
  ![Platsen för konfigurationsfilen diagnostik](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
  Om du inte hittar filen finns information om hur du aktiverar tillägget diagnostik i projektet molntjänster [konfigurera diagnostik för virtuella datorer och Azure Cloud Services](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

3. Lägg till följande `SinksConfig` avsnitt som ett underordnat element för `WadCfg`:  
  ```xml
  <WadCfg>
    <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
    <SinksConfig>
      <Sink name="MyApplicationInsightsProfiler">
        <!-- Replace with your own Application Insights instrumentation key. -->
        <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
      </Sink>
    </SinksConfig>
  </WadCfg>
  ```

> [!NOTE]  
> Om den `diagnostics.wadcfgx` filen innehåller också en annan mottagare av typen `ApplicationInsights`, alla tre av dessa instrumentation nycklar måste matcha:  
>  * Instrumentation nyckeln används av ditt program.  
>  * Instrumentation nyckel som används av den `ApplicationInsights` mottagare.  
>  * Instrumentation nyckel som används av den `ApplicationInsightsProfiler` mottagare.  
>
> Du kan hitta faktiska instrumentation värdet för nyckeln används av den `ApplicationInsights` sink i ServiceConfiguration.\*. cscfg-filer.  
> När Visual Studio 15,5 Azure SDK-utgåvan instrumentation nycklarna som används av programmet och `ApplicationInsightsProfiler` sink måste matcha varandra.


## <a name="environment-deployment-and-runtime-configurations"></a>Konfigurationer för distribution och körning av miljö

1. Distribuera distributionsdefinitionen ändrade miljö.  

  Om du vill göra ändringar, vanligtvis en fullständig mall-distribution eller en molntjänster som har publicera via PowerShell-cmdletar eller Visual Studio ingår.  

  Följande är en alternativ metod för befintliga virtuella datorer som vidrör endast Azure-diagnostik filnamnstillägget:  
  ```powershell
  $ConfigFilePath = [IO.Path]::GetTempFileName()
  # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
  (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
  # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
  # if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
  # (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
  Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
  ```

2. Om den avsedda programmet körs [IIS](https://www.microsoft.com/web/platform/server.aspx), aktivera den `IIS Http Tracing` Windows-funktionen:  
  
  1. Upprätta fjärråtkomst till miljön och sedan använda den [lägga till Windows-funktioner]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) fönster eller kör följande kommando i PowerShell (som administratör):  
    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
  2. Om etablera fjärråtkomst är ett problem, kan du använda [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) att köra följande kommando:  
    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-the-profiler-on-on-premises-servers"></a>Aktivera profileraren på lokala servrar

Aktivera profileraren på en lokal server kallas även körs Application Insights-Profiler i fristående läge (inte är den kopplad till Azure-diagnostik tillägget ändringar). 

Vi har inga planer på att officiellt ge support profileraren för lokala servrar. Om du vill experimentera med det här scenariot kan du [hämtar support](https://github.com/ramach-msft/AIProfiler-Standalone). Vi kan *inte* ansvarar för underhållet av koden eller för att svara på problem och funktionsförfrågningar som rör koden.

## <a name="next-steps"></a>Nästa steg

- Skapa trafik till ditt program (till exempel starta en [tillgänglighetstest](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vänta 10 – 15 minuter för spårningar att starta skickas till Application Insights-instans.
- Se [Profiler-spårningar](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) i Azure-portalen.
- Få hjälp med felsökning av problem med profiler i [profileraren felsökning](app-insights-profiler.md#troubleshooting).
- Läs mer om profileraren i [Application Insights Profiler](app-insights-profiler.md).
