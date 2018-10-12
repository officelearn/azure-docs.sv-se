---
title: Aktivera Application Insights Profiler för program som finns på Azure Cloud Services-resurser | Microsoft Docs
description: Lär dig mer om att ställa in Application Insights Profiler i ett program som körs på Azure Cloud Services.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/16/2017
ms.reviewer: ramach
ms.author: mbullwin
ms.openlocfilehash: eb2ec0c0b77e71a54d1e7f852a22d82203abf7b6
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091988"
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Aktivera Application Insights Profiler för virtuella Azure-datorer, Service Fabric och Azure Cloud Services

Den här artikeln visar hur du aktiverar Azure Application Insights Profiler för ett ASP.NET-program som är värd för en Azure Cloud Services-resurs.

Exemplen i den här artikeln har stöd för Azure Virtual Machines, VM-skalningsuppsättningar, Azure Service Fabric och Azure Cloud Services. Exemplen är beroende av mallar som har stöd för den [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) distributionsmodell.  


## <a name="overview"></a>Översikt

Följande bild visar hur Application Insights Profiler fungerar med program som finns på Azure Cloud Services-resurser. Azure Cloud Services-resurser är virtuella datorer, skalningsuppsättningar, molntjänster och Service Fabric-kluster. Avbildningen används en Azure virtuell dator som exempel.  

  ![Diagram som visar hur Application Insights Profiler fungerar med Azure Cloud Services-resurser](./media/enable-profiler-compute/overview.png)

Innan du kan utnyttja Profiler, måste du ändra konfigurationen på tre platser:

* Fönstret Application Insights-instans i Azure-portalen.
* Programmets källkod (till exempel ASP.NET-webbprogram).
* Miljö distribution definition källkoden (till exempel en Azure Resource Manager-mall i JSON-fil).


## <a name="set-up-the-application-insights-instance"></a>Konfigurera Application Insights-instans

1. [Skapa en ny Application Insights-resurs](https://docs.microsoft.com/azure/application-insights/app-insights-create-new-resource), eller välj en befintlig. 

1. Gå till Application Insights-resursen och sedan kopiera instrumenteringsnyckeln.

   ![Platsen för instrumenteringsnyckeln](./media/enable-profiler-compute/CopyAIKey.png)

1. Om du vill slutföra konfigurationen av Application Insights-instans för Profiler, slutför du proceduren som beskrivs i [aktivera Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler). Du behöver inte att länka webbappar, eftersom steg som är specifika för app services-resursen. Kontrollera att Profiler är aktiverat i den **konfigurera Profiler** fönstret.


## <a name="set-up-the-application-source-code"></a>Ställ in programmets källkod

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>ASP.NET-webbprogram, Azure Cloud Services web-roller eller Service Fabric ASP.NET klientdelen
Konfigurera programmet skicka telemetridata till en Application Insights-instans på varje `Request` igen.  

Lägg till den [Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) till projektet. Se till att versionerna som NuGet-paketet finns på följande sätt:  
  - För ASP.NET-program: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 eller senare.
  - För ASP.NET Core-program: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 eller senare.
  - För andra .NET och .NET Core-program (till exempel en tillståndslös Service Fabric-tjänst eller en Cloud Services-arbetsroll): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) eller [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 eller senare.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Azure Cloud Services worker-roller eller Service Fabric tillståndslösa serverdelen
Förutom att du har slutfört föregående steg om ditt program är *inte* en ASP.NET eller ASP.NET Core-program (till exempel, om det är en Azure Cloud Services-arbetsroll eller Service Fabric tillståndslösa API: er), gör du följande:  

  1. Lägg till följande kod tidigt i program-livstid:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Läs mer om den här nyckeln globala instrumentation är konfigurerad, [används Service Fabric med Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. För alla typer av kod som du vill att instrumentera, lägga till en `StartOperation<RequestTelemetry>` **USING** instruktionen runt den som visas i följande exempel:

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

        Anropa `StartOperation<RequestTelemetry>` inom en annan `StartOperation<RequestTelemetry>` området stöds inte. Du kan använda `StartOperation<DependencyTelemetry>` i den kapslade omfång i stället. Exempel:  
        
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

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurera distributionsdefinitionen miljö

Miljön där Profiler och kör ditt program kan vara en virtuell dator, en VM-skalningsuppsättning, Service Fabric-kluster eller en cloud services-instans.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Virtuella datorer, skalningsuppsättningar eller Service Fabric

Fullständigt exempel finns:  
  * [Virtuell dator](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Virtual machine scale Sets](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric-kluster](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Om du vill konfigurera din miljö, gör du följande:
1. Så att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare, så är tillräckliga för att bekräfta att det distribuerade Operativsystemet är `Windows Server 2012 R2` eller senare.

1. Sök efter den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) tillägget på distributionsmallen för filen och Lägg sedan till följande `SinksConfig` avsnittet som ett underordnat element av `WadCfg`. Ersätt den `ApplicationInsightsProfiler` egenskapsvärdet med din egen Application Insights-instrumenteringsnyckeln:  

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

      Information om att lägga till Diagnostics-tillägg till mallen för distribution finns i [Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> För virtuella datorer är ett alternativ till ovanstående json-baserade att navigera i Azure portal för att **virtuella datorer** > **diagnostikinställningar**  >   **Egenskaperna** > Set skicka diagnostiska data till Application Insights till **aktiverad** och välj antingen ett Application Insights-konto eller en specifik ikey.

### <a name="azure-cloud-services"></a>Azure Cloud Services

1. Så att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare, så är tillräckliga för att bekräfta att den *ServiceConfiguration.\*.cscfg* filer har en `osFamily` värdet av ”5” eller senare.

1. Leta upp den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* filen för din programroll som visas här:  

   ![Platsen för konfigurationsfilen diagnostik](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Om du inte hittar filen, information om hur du aktivera tillägget diagnostik i Azure Cloud Services-projektet finns [konfigurera diagnostik för Azure Cloud Services och virtuella datorer](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

1. Lägg till följande `SinksConfig` avsnittet som ett underordnat element av `WadCfg`:  

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
> Om den *diagnostics.wadcfgx* -filen innehåller också en annan mottagare av typen `ApplicationInsights`, alla tre av följande instrumenteringsnycklar måste matcha:  
>  * Den nyckel som används av ditt program.  
>  * Den nyckel som används av den `ApplicationInsights` mottagare.  
>  * Den nyckel som används av den `ApplicationInsightsProfiler` mottagare.  
>
> Du kan hitta faktiska instrumentation nyckelvärdet som används av den `ApplicationInsights` mottagare den *ServiceConfiguration.\*.cscfg* filer.  
> När du har Visual Studio 15.5 Azure SDK-versionen kan endast instrumenteringsnycklar som används av programmet och `ApplicationInsightsProfiler` mottagare behöver matchar varandra.


## <a name="configure-environment-deployment-and-runtime"></a>Konfigurera miljön distribution och körning

1. Distribuera distributionsdefinitionen ändrade miljö.  

   Att göra ändringar, innefattar vanligen en fullständig mall-distribution eller ett moln-tjänster baserat publicera via PowerShell-cmdletar eller Visual Studio.  

   Följande är en alternativ metod för befintliga virtuella datorer som rör vid endast Azure Diagnostics-tillägg:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Om det avsedda programmet körs [IIS](https://www.microsoft.com/web/downloads/platform.aspx), aktivera den `IIS Http Tracing` Windows-funktionen genom att göra följande:  

   a. Upprätta fjärråtkomst till miljön och sedan använda den [lägga till Windows-funktioner]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) fönster och kör sedan följande kommando i PowerShell (som administratör):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Om upprätta fjärråtkomst har uppstått ett problem, kan du använda [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) att köra följande kommando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Aktivera Profiler på lokala servrar

Att aktivera Profiler på en lokal server kallas även köra Application Insights Profiler i fristående läge. Den är inte kopplad till Azure Diagnostics-tillägget ändringar.

Vi har inga planer på att officiellt ge support till Profiler för lokala servrar. Om du är intresserad av att experimentera med det här scenariot kan du [hämta supportkod](https://github.com/ramach-msft/AIProfiler-Standalone). Vi kan *inte* ansvarar för att underhålla koden eller för att svara på problem och vilka funktionsförfrågningar som är relaterade till koden.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program (till exempel starta en [tillgänglighetstestet](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vänta 10 till 15 minuter innan spårningarna starta som ska skickas till Application Insights-instans.
- Se [Profiler-spårningar](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) i Azure-portalen.
- Få hjälp med felsökning av problem med profiler i [Profiler felsökning](app-insights-profiler.md#troubleshooting).
- Läs mer om Profiler i [Application Insights Profiler](app-insights-profiler.md).
