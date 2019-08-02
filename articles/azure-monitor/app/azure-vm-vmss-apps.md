---
title: Övervaka program prestanda som finns på Azure VM och skalnings uppsättningar för virtuella Azure-datorer | Microsoft Docs
description: Övervakning av program prestanda för virtuella Azure-datorer och skalnings uppsättningar för virtuella Azure-datorer. Diagrammets inläsnings-och svars tid, beroende information och ange aviseringar för prestanda.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: f2c6b98fd0be2061e9d8cab5c063cafadf71476a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597449"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>Övervaka program prestanda som finns på Azure VM och skalnings uppsättningar för virtuella Azure-datorer

Nu är det enklare än någonsin att aktivera övervakning av .NET-baserade webb program som körs på [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) och [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) . Få alla fördelar med att använda Application Insights utan att ändra koden.

Den här artikeln vägleder dig genom att aktivera Application Insights övervakning med ApplicationMonitoringWindows-tillägget och tillhandahålla preliminär vägledning för att automatisera processen för storskaliga distributioner.

> [!IMPORTANT]
> Azure ApplicationMonitoringWindows-tillägget är för närvarande en offentlig för hands version.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar den inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds och vissa kan ha begränsade funktioner.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Aktivera Application Insights

Det finns två sätt att aktivera program övervakning för virtuella Azure-datorer och program för skalnings uppsättningar för virtuella Azure-datorer:

* **Agent-baserad program övervakning** (ApplicationMonitoringWindows-tillägg).
    * Den här metoden är det enklaste sättet att aktivera, och ingen Avancerad konfiguration krävs. Det kallas ofta övervakning av körnings miljön. För virtuella Azure-datorer och skalnings uppsättningar för virtuella Azure-datorer rekommenderar vi att du minst aktiverar den här övervaknings nivån. Efter det, baserat på ditt speciella scenario, kan du utvärdera om du behöver manuell Instrumentation.
    * För närvarande stöds endast .NET IIS-värdbaserade program.

* Att **instrumentera programmet manuellt** genom att installera Application Insights SDK.

    * Den här metoden är mycket mer anpassningsbar, men kräver [att du lägger till ett beroende på Application Insights SDK NuGet-paket](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Den här metoden innebär också att du måste hantera uppdateringarna till den senaste versionen av paketen själv.

    * Om du behöver göra anpassade API-anrop för att spåra händelser/beroenden som inte har registrerats som standard med en agent-baserad övervakning, behöver du använda den här metoden. Mer information finns i [artikeln om API för anpassade händelser och mått](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) .

> [!NOTE]
> Om både agentbaserade övervakning och manuellt SDK-baserad Instrumentation identifieras, kommer de manuella Instrumentation inställningarna att påverkas. Detta är för att förhindra att duplicerade data skickas. Mer information om det här finns i [fel söknings avsnittet](#troubleshooting) nedan.

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>Hantera agentbaserade övervakning av .NET-program på en virtuell dator med hjälp av PowerShell

Installera eller uppdatera tillägg för program övervakning för virtuell dator
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

Avinstallera tillägg för program övervakning från virtuell dator
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Fråga om program övervaknings tilläggets status för virtuell dator
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Hämta lista över installerade tillägg för virtuell dator
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>Hantera agentbaserade övervakning av .NET-program på skalnings uppsättningen för virtuella Azure-datorer med hjälp av PowerShell

Installera eller uppdatera tillägget för program övervakning för skalnings uppsättningen för virtuella Azure-datorer
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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

Avinstallera tillägg för program övervakning från skalnings uppsättningen för virtuella Azure-datorer
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Fråga om program övervaknings tilläggets tilläggs status för skalnings uppsättningen för den virtuella Azure-datorn
```powershell
# Not supported by extensions framework
```

Hämta lista över installerade tillägg för skalnings uppsättningen för virtuella Azure-datorer
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Felsökning

Nedan visas vår stegvisa fel söknings guide för den tilläggbaserade övervakningen av .NET-program som körs på virtuella Azure-datorer och skalnings uppsättningar för virtuella Azure-datorer.

> [!NOTE]
> .NET Core-, Java-och Node. js-program stöds bara på virtuella Azure-datorer och skalnings uppsättningar för virtuella Azure-datorer via manuell SDK-baserad Instrumentation, och därför gäller inte stegen nedan för dessa scenarier.

Utökning av utdata loggas till filer som finns i följande kataloger:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [distribuerar ett program till en skalnings uppsättning för en virtuell Azure-dator](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Konfigurera](monitor-web-app-availability.md) webbtester för tillgänglighet om du vill få en avisering om slut punkten är nere.
