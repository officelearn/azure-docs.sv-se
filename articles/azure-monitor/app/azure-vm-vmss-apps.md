---
title: Övervaka prestanda på virtuella Azure-datorer – Azure Application Insights
description: Programprestandaövervakning för Azure VM- och Azure-skalningsuppsättningar för virtuella datorer. Diagraminläsning och svarstid, beroendeinformation och ange aviseringar om prestanda.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661336"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Distribuera Azure Monitor Application Insights Agent på virtuella Azure-datorer och Azure-skalningsuppsättningar för virtuella datorer

Det är nu enklare än någonsin att aktivera övervakning på dina .NET-baserade webbprogram som körs på [virtuella Azure-datorer](https://azure.microsoft.com/services/virtual-machines/) och [Azure-skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) för virtuella datorer. Få alla fördelar med att använda Application Insights utan att ändra koden.

I den här artikeln får du hjälp med att aktivera programstatistikövervakning med hjälp av Application Insights-agenten och ger preliminär vägledning för automatisering av processen för storskaliga distributioner.

> [!IMPORTANT]
> Azure Application Insights Agent för .NET är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett servicenivåavtal och vi rekommenderar den inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds och vissa kan ha begränsade funktioner.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Aktivera Application Insights

Det finns två sätt att aktivera programövervakning för virtuella Azure-datorer och Azure-skalningsuppsättningar för virtuella datorer:

* **Kodlös** via Application Insights Agent
    * Den här metoden är den enklaste att aktivera och ingen avancerad konfiguration krävs. Det kallas ofta "runtime" övervakning.

    * För virtuella Azure-datorer och Azure-skalningsuppsättningar för virtuella datorer rekommenderar vi att du åtminstone aktiverar den här övervakningsnivån. Därefter, baserat på ditt specifika scenario, kan du utvärdera om manuell instrumentering behövs.

    * Application Insights Agent samlar in samma beroendesignaler direkt som .NET SDK. Se [Automatisk samling för beroende om](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net) du vill veta mer.
        > [!NOTE]
        > För närvarande stöds endast .Net IIS-värdbaserade program. Använd ett SDK för att instrumentera ASP.NET Core-, Java- och Node.js-program som finns på en Azure-virtuella datorer och skalningsuppsättningar för virtuella datorer.

* **Kodbaserad** via SDK

    * Den här metoden är mycket mer anpassningsbar, men det kräver [att du lägger till ett beroende av Application Insights SDK NuGet-paketen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Den här metoden innebär också att du själv måste hantera uppdateringarna till den senaste versionen av paketen.

    * Om du behöver göra anpassade API-anrop för att spåra händelser/beroenden som inte fångas in som standard med agentbaserad övervakning, måste du använda den här metoden. Kolla in [API för anpassade händelser och mått artikel](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) om du vill veta mer.

> [!NOTE]
> Om både agentbaserad övervakning och manuell SDK-baserad instrumentering upptäcks kommer endast inställningarna för manuell instrumentering att uppfyllas. Detta för att förhindra att dubblettdata skickas. Om du vill veta mer om detta kan du läsa [felsökningsavsnittet](#troubleshooting) nedan.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Hantera Application Insights Agent för .NET-program på virtuella Azure-datorer med PowerShell

> [!NOTE]
> Innan du installerar Application Insights Agent behöver du en anslutningssträng. [Skapa en ny Application Insights-resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) eller kopiera anslutningssträngen från en befintlig programinsiktsresurs.

> [!NOTE]
> Ny till powershell? Kolla in [komma igång guide](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

Installera eller uppdatera Application Insights Agent som ett tillägg för virtuella Azure-datorer
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Du kan installera eller uppdatera Application Insights Agent som ett tillägg över flera virtuella datorer i skala med hjälp av en Powershell-loop.

Avinstallera application insights-agenttillägg från den virtuella Azure-datorn
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Frågeprogramstatistikagenttilläggsstatus för virtuell Azure-dator
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Hämta lista över installerade tillägg för virtuell Azure-dator
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Du kan också visa installerade tillägg i [Azure-bladet för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) i portalen.

> [!NOTE]
> Verifiera installationen genom att klicka på Live Metrics Stream i application insights-resursen som är associerad med anslutningssträngen som du använde för att distribuera Application Insights Agent-tillägget. Om du skickar data från flera virtuella datorer väljer du virtuella azure-datorer under Servernamn. Det kan ta upp till en minut innan data börjar flöda.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Hantera Application Insights Agent för .NET-program på Azure-skalningsuppsättningar för virtuella datorer med powershell

Installera eller uppdatera Application Insights Agent som ett tillägg för Azure-skalningsuppsättning för virtuella datorer
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Avinstallera programövervakningstillägg från Azure-skalningsuppsättningar för virtuella datorer
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Frågeprogramövervakningstilläggsstatus för Azure-skalningsuppsättningar för virtuella datorer
```powershell
# Not supported by extensions framework
```

Hämta lista över installerade tillägg för Azure-skalningsuppsättningar för virtuella datorer
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Felsökning

Hitta felsökningstips för Program Insights Monitoring Agent Extension för .NET-program som körs på virtuella Azure-datorer och skalningsuppsättningar för virtuella datorer.

> [!NOTE]
> .NET Core-, Java- och Node.js-program stöds endast på virtuella Azure-datorer och Azure-skalningsuppsättningar för virtuella datorer via manuell SDK-baserad instrumentering och därför gäller inte stegen nedan för dessa scenarier.

Utdata för tilläggskörning loggas till filer som finns i följande kataloger:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [distribuerar ett program till en Azure virtuell dator skalningsuppsättning](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Konfigurera webbtester för tillgänglighet](monitor-web-app-availability.md) som ska aviseras om slutpunkten är nere.
