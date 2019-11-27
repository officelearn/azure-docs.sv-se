---
title: Få underhålls meddelanden för virtuella Azure-datorer med PowerShell
description: Visa underhålls meddelanden för virtuella datorer som körs i Azure och starta självbetjänings underhåll med hjälp av PowerShell.
services: virtual-machines
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: f136fc3001e6ae224e264a59ceba66ed61ead865
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535839"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Hantera planerat underhåll med PowerShell

**Den här artikeln gäller för virtuella datorer som kör både Linux och Windows.**

Du kan använda Azure PowerShell för att se när virtuella datorer är schemalagda för [Underhåll](maintenance-notifications.md). Information om planerat underhåll är tillgängligt från cmdleten [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) när du använder parametern `-status`.
  
Underhålls information returneras endast om underhållet är planerat. Om inget underhåll har schemalagts som påverkar den virtuella datorn returnerar cmdleten ingen underhålls information. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

Följande egenskaper returneras under MaintenanceRedeployStatus: 

| Value | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta underhåll på den virtuella datorn just nu |
| PreMaintenanceWindowStartTime         | Början av självbetjänings fönstret för underhåll när du kan initiera underhåll på den virtuella datorn |
| PreMaintenanceWindowEndTime           | Slutet av självbetjänings fönstret för underhåll när du kan initiera underhåll på den virtuella datorn |
| MaintenanceWindowStartTime            | Början av det underhåll som schemalagts för Azure initierar underhåll på den virtuella datorn |
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhålls perioden i vilken Azure initierar underhåll på den virtuella datorn |
| LastOperationResultCode               | Resultatet av det senaste försöket att initiera underhåll på den virtuella datorn |



Du kan också hämta underhålls status för alla virtuella datorer i en resurs grupp med hjälp av [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) och inte ange en virtuell dator.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

Följande PowerShell-exempel tar ditt prenumerations-ID och returnerar en lista över virtuella datorer som är schemalagda för underhåll.

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

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Starta underhåll på den virtuella datorn med PowerShell

Med hjälp av informationen från funktionen i föregående avsnitt, påbörjas följande underhåll på en virtuell dator om **IsCustomerInitiatedMaintenanceAllowed** är inställt på True.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klassiska distributioner

Om du fortfarande har äldre virtuella datorer som har distribuerats med den klassiska distributions modellen kan du använda PowerShell för att fråga efter virtuella datorer och initiera underhåll.

Om du vill hämta underhålls statusen för en virtuell dator skriver du:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Starta underhållet på den klassiska virtuella datorn genom att skriva:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Nästa steg

Du kan också hantera planerat underhåll med hjälp av [Azure CLI](maintenance-notifications-cli.md) eller [portalen](maintenance-notifications-portal.md).
