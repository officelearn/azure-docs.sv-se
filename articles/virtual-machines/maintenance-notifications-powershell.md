---
title: Få underhållsmeddelanden för virtuella Azure-datorer med PowerShell
description: Visa underhållsmeddelanden för virtuella datorer som körs i Azure och starta självbetjäningsunderhåll med PowerShell.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916090"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Hantera planerat underhåll med PowerShell

**Den här artikeln gäller virtuella datorer som kör både Linux och Windows.**

Du kan använda Azure PowerShell för att se när virtuella datorer är schemalagda för [underhåll](maintenance-notifications.md). Planerad underhållsinformation är tillgänglig från [Get-AzVM-cmdleten](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) när du använder parametern. `-status`
  
Underhållsinformation returneras endast om underhåll planeras. Om inget underhåll har schemalagts som påverkar den virtuella datorn returnerar cmdleten ingen underhållsinformation. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

Följande egenskaper returneras under MaintenanceRedeployStatus: 

| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceTilläms | Anger om du kan starta underhåll på den virtuella datorn just nu |
| PreMaintenanceWindowStartTime         | Början av underhålls-självbetjäningsfönstret när du kan initiera underhåll på din virtuella dator |
| PreMaintenanceWindowEndTime           | Slutet av underhållsfönstret för självbetjäning när du kan initiera underhåll på din virtuella dator |
| UnderhållWindowStartTime            | Början av det planerade underhållet där Azure initierar underhåll på din virtuella dator |
| UnderhållWindowEndTime              | Slutet av det planerade fönstret för underhåll där Azure initierar underhåll på din virtuella dator |
| LastOperationResultCode               | Resultatet av det senaste försöket att initiera underhåll på den virtuella datorn |



Du kan också hämta underhållsstatus för alla virtuella datorer i en resursgrupp genom att använda [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) och inte ange en virtuell dator.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

I följande PowerShell-exempel tar ditt prenumerations-ID och returnerar en lista över virtuella datorer som är schemalagda för underhåll.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Starta underhållet på den virtuella datorn med PowerShell

Med hjälp av information från funktionen i föregående avsnitt startar följande underhåll på en virtuell dator om **IsCustomerInitiatedMaintenanceAllowed** är inställt på true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klassiska distributioner

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Om du fortfarande har äldre virtuella datorer som har distribuerats med den klassiska distributionsmodellen kan du använda PowerShell för att fråga efter virtuella datorer och initiera underhåll.

Om du vill hämta underhållsstatus för en virtuell dator skriver du:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Om du vill starta underhållet på den klassiska virtuella datorn skriver du:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Nästa steg

Du kan också hantera planerat underhåll med Hjälp av [Azure CLI](maintenance-notifications-cli.md) eller [portalen](maintenance-notifications-portal.md).
