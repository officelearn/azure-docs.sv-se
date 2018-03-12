---
title: "Aktivera Application Insights Profiler för program som finns på Azure Cloud Services resurser | Microsoft Docs"
description: "Lär dig hur du ställer in Application Insights Profiler i ett program som körs på Azure-molntjänster."
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
ms.openlocfilehash: a24695f7bbb5fb0546e27c934319a60a3418b9e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Aktivera Application Insights Profiler för virtuella Azure-datorer, Service Fabric och Azure-molntjänster

Den här artikeln visar hur du aktiverar Azure Application Insights Profiler på ett ASP.NET-program som finns i en Azure Cloud Services-resurs.

Exemplen i den här artikeln har stöd för Azure virtuella datorer, virtuella datorer, Azure Service Fabric och Azure Cloud Services. Exemplen är beroende av mallar som har stöd för den [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) distributionsmodell.  


## <a name="overview"></a>Översikt

Följande bild visar hur Application Insights Profiler fungerar med program som finns på Azure Cloud Services resurser. Azure Cloud Services resurser inkluderar virtuella datorer, skalningsuppsättningar, cloud services och Service Fabric-kluster. Bilden används en virtuell Azure-dator som exempel.  

  ![Diagram över hur Application Insights Profiler fungerar med Azure Cloud Services-resurser](./media/enable-profiler-compute/overview.png)

Om du vill aktivera profileraren helt måste du ändra konfigurationen på tre platser:

* Fönstret Application Insights-instans i Azure-portalen.
* Programmets källkod (exempelvis en ASP.NET-webbprogram).
* Miljö distribution definition källkoden (till exempel en Azure Resource Manager-mall i JSON-fil).


## <a name="set-up-the-application-insights-instance"></a>Konfigurera Application Insights-instans

1. [Skapa en ny Application Insights-resurs](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-create-new-resource), eller välj en befintlig. 

2. Gå till Application Insights-resurs och kopiera nyckeln instrumentation.

   ![Platsen för nyckeln instrumentation](./media/enable-profiler-compute/CopyAIKey.png)

3. Om du vill slutföra konfigureringen av Application Insights-instans för profileraren under proceduren som beskrivs i [aktivera Profiler. Du behöver inte länka webbappar, eftersom steg som är specifika för resursen app services. Kontrollera att profileraren har aktiverats i den **konfigurera Profiler** fönstret.


## <a name="set-up-the-application-source-code"></a>Ställ in programmets källkod

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>ASP.NET-webbprogram, Azure Cloud Services web-roller eller frontwebb Service Fabric ASP.NET
Konfigurera programmet att skicka telemetridata till en Application Insights-instans på varje `Request` igen.  

Lägg till den [Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) till projektet. Kontrollera att NuGet-paketet versioner är följande:  
  - För ASP.NET-program: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 eller senare.
  - För program som ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 eller senare.
  - För andra .NET och .NET Core program (till exempel en tillståndslös Service Fabric-tjänst eller en arbetsroll i Cloud Services): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) eller [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 eller senare.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Azure Cloud Services arbetarroller eller Service Fabric tillståndslös serverdelen
Förutom slutfört föregående steg, om ditt program är *inte* ett ASP.NET eller ASP.NET Core program (till exempel, om det är en Azure Cloud Services worker-rollen eller Service Fabric tillståndslös API: er), gör du följande:  

  1. Lägg till följande kod tidigt i program-livstid:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Mer information om den här nyckeln konfigurationen globala instrumentation finns [används Service Fabric med Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2. För varje typ av kod som du vill instrumentera, lägga till en `StartOperation<RequestTelemetry>` **USING** instruktionen runt det, som visas i följande exempel:

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

Miljön där Profiler och kör ditt program kan vara en virtuell dator, en skaluppsättning för virtuell dator, ett Service Fabric-kluster eller ett moln services-instans.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Skaluppsättningar, virtuella datorer eller Service Fabric

Kompletta exempel finns:  
  * [Virtuell dator](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Virtuella datorns skaluppsättning](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric-kluster](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Om du vill konfigurera din miljö gör du följande:
1. Att se till att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare, det räcker att bekräfta att det distribuerade Operativsystemet är `Windows Server 2012 R2` eller senare.

2. Sök efter den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) tillägget på distributionsmallen för filen och Lägg sedan till följande `SinksConfig` avsnitt som ett underordnat element för `WadCfg`. Ersätt den `ApplicationInsightsProfiler` egenskapsvärde med din egen Application Insights instrumentation nyckel:  

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

> [!TIP]
> För virtuella datorer som ett alternativ till json-baserade stegen ovan är att gå till Azure portal **virtuella datorer** > **diagnostikinställningar**  >   **Egenskaperna** > Set skicka diagnostikdata till Application Insights i **aktiverad** och välj antingen ett Application Insights-konto eller en specifik ikey.

### <a name="azure-cloud-services"></a>Azure Cloud Services

1. Att se till att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare, det räcker att bekräfta att den *ServiceConfiguration.\*. cscfg* filer har en `osFamily` värdet ”5” eller senare.

2. Leta upp den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* filen för ditt program, som visas här:  

   ![Platsen för konfigurationsfilen diagnostik](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Om du inte hittar filen finns information om hur du aktiverar tillägget diagnostik i projektet Azure Cloud Services [konfigurera diagnostik för virtuella datorer och Azure Cloud Services](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

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
> Om den *diagnostics.wadcfgx* filen innehåller också en annan mottagare av typen `ApplicationInsights`, alla tre av följande instrumentation nycklar måste matcha:  
>  * Den nyckel som används av ditt program.  
>  * Den nyckel som används av den `ApplicationInsights` mottagare.  
>  * Den nyckel som används av den `ApplicationInsightsProfiler` mottagare.  
>
> Du kan hitta faktiska instrumentation värdet för nyckeln som används av den `ApplicationInsights` sink den *ServiceConfiguration.\*. cscfg* filer.  
> När Azure SDK för Visual Studio 15,5 utgåvan endast instrumentation nycklarna som används av programmet och `ApplicationInsightsProfiler` sink måste matcha varandra.


## <a name="configure-environment-deployment-and-runtime"></a>Konfigurera miljön distribution och körning

1. Distribuera distributionsdefinitionen ändrade miljö.  

   Att göra ändringar, är oftast att en fullständig mall-distribution eller ett moln-tjänster baserat publicera via PowerShell-cmdlets eller Visual Studio.  

   Följande är en alternativ metod för befintliga virtuella datorer som rör vid Azure-diagnostik tillägget:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. Om den avsedda programmet körs [IIS](https://www.microsoft.com/web/platform/server.aspx), aktivera den `IIS Http Tracing` Windows-funktionen genom att göra följande:  

   a. Upprätta fjärråtkomst till miljön och sedan använda den [lägga till Windows-funktioner]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) fönster eller kör följande kommando i PowerShell (som administratör):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Om etablera fjärråtkomst är ett problem, kan du använda [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) att köra följande kommando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Aktivera Profiler på lokala servrar

Aktiverar Profiler på en lokal server kallas även körs Application Insights-Profiler i fristående läge. Den är inte kopplad till Azure-diagnostik tillägget ändringar.

Vi har inga planer på att officiellt ge support Profiler för lokala servrar. Om du vill experimentera med det här scenariot kan du [hämtar support](https://github.com/ramach-msft/AIProfiler-Standalone). Vi kan *inte* ansvarar för underhållet av koden eller för att svara på problem och funktionsförfrågningar som är relaterade till koden.

## <a name="next-steps"></a>Nästa steg

- Skapa trafik till ditt program (till exempel starta en [tillgänglighetstest](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vänta 10 – 15 minuter för spårningar att starta skickas till Application Insights-instans.
- Se [Profiler-spårningar](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) i Azure-portalen.
- Få hjälp med felsökning av problem med profiler i [profileraren felsökning](app-insights-profiler.md#troubleshooting).
- Läs mer om Profiler i [Application Insights Profiler](app-insights-profiler.md).
