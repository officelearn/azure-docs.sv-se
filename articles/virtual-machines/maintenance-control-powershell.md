---
title: Underhållskontroll för virtuella Azure-datorer med PowerShell
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med hjälp av underhållskontroll och PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060137"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Förhandsversion: Styra uppdateringar med underhållskontroll och Azure PowerShell

Hantera plattformsuppdateringar, som inte kräver en omstart, med hjälp av underhållskontroll. Azure uppdaterar ofta sin infrastruktur för att förbättra tillförlitlighet, prestanda, säkerhet eller starta nya funktioner. De flesta uppdateringar är transparenta för användarna. Vissa känsliga arbetsbelastningar, som spel, mediestreaming och ekonomiska transaktioner, kan inte tolerera ens några sekunder av en vm-frysning eller frånkoppling för underhåll. Underhållskontroll ger dig möjlighet att vänta på plattformsuppdateringar och tillämpa dem inom ett rullande 35-dagars fönster. 

Med underhållskontrollen kan du bestämma när du ska installera uppdateringar på dina isolerade virtuella datorer.

Med underhållskontroll kan du:
- Batchuppdateringar till ett uppdateringspaket.
- Vänta upp till 35 dagar med att installera uppdateringar. 
- Automatisera plattformsuppdateringar för ditt underhållsfönster med Azure Functions.
- Underhållskonfigurationer fungerar över prenumerationer och resursgrupper. 

> [!IMPORTANT]
> Underhållskontroll är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Begränsningar

- Virtuella datorer måste finnas på en [dedikerad värd](./linux/dedicated-hosts.md)eller skapas med en [isolerad VM-storlek](./linux/isolation.md).
- Efter 35 dagar tillämpas en uppdatering automatiskt.
- Användaren måste ha åtkomst till **resursdeltagare.**


## <a name="enable-the-powershell-module"></a>Aktivera PowerShell-modulen

Se `PowerShellGet` till att den är uppdaterad.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az.Maintenance PowerShell-cmdlets är i förhandsversion, så du `AllowPrerelease` måste installera modulen med parametern i Cloud Shell eller din lokala PowerShell-installation.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Om du installerar lokalt kontrollerar du att du öppnar powershell-prompten som administratör.

Du kan också bli ombedd att bekräfta att du vill installera från en *ej betrodd databas*. Skriv `Y` eller välj **Ja till alla** för att installera modulen.



## <a name="create-a-maintenance-configuration"></a>Skapa en underhållskonfiguration

Skapa en resursgrupp som en behållare för konfigurationen. I det här exemplet skapas en resursgrupp med namnet *myMaintenanceRG* i *eastus*. Om du redan har en resursgrupp som du vill använda kan du hoppa över den här delen och ersätta resursgruppsnamnet med ditt eget i resten av exemplen.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Använd [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) för att skapa en underhållskonfiguration. I det här exemplet skapas en underhållskonfiguration med namnet *myConfig-begränsad* till värden. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Med `-MaintenanceScope host` hjälp säkerställer att underhållskonfigurationen används för att styra uppdateringar till värden.

Om du försöker skapa en konfiguration med samma namn, men på en annan plats, får du ett felmeddelande. Konfigurationsnamnen måste vara unika för din prenumeration.

Du kan fråga efter tillgängliga underhållskonfigurationer med [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Tilldela konfigurationen

Använd [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) för att tilldela konfigurationen till din isolerade virtuella dator eller Azure Dedikerad värd.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Tillämpa konfigurationen på en virtuell dator med konfigurationens ID. Ange `-ResourceType VirtualMachines` och ange namnet på `-ResourceName`den virtuella datorn för `-ResourceGroupName`och resursgruppen för den virtuella datorn för . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Dedikerad värd

Om du vill använda en konfiguration på en `-ResourceType hosts` `-ResourceParentName` dedikerad värd måste du `-ResourceParentType hostGroups`också inkludera , med namnet på värdgruppen och . 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Sök efter väntande uppdateringar

Använd [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) för att se om det finns väntande uppdateringar. Används `-subscription` för att ange Azure-prenumerationen för den virtuella datorn om den skiljer sig från den som du är inloggad på.

Om det inte finns några uppdateringar att visa returnerar det här kommandot ingenting. Annars returneras ett PSApplyUpdate-objekt:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Isolerad virtuell dator

Sök efter väntande uppdateringar för en isolerad virtuell dator. I det här exemplet formateras utdata som en tabell för läsbarhet.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Dedikerad värd

Så här söker du efter väntande uppdateringar för en dedikerad värd. I det här exemplet formateras utdata som en tabell för läsbarhet. Ersätt värdena för resurserna med dina egna.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Tillämpa uppdateringar

Använd [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) för att tillämpa väntande uppdateringar.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Skapa en begäran om att installera uppdateringar på en isolerad virtuell dator.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

När det här kommandot `PSApplyUpdate` lyckas returneras ett objekt. Du kan använda attributet `Get-AzApplyUpdate` Name i kommandot för att kontrollera uppdateringsstatusen. Se [Kontrollera uppdateringsstatus](#check-update-status).

### <a name="dedicated-host"></a>Dedikerad värd

Installera uppdateringar för en dedikerad värd.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Kontrollera uppdateringsstatus
Använd [Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) för att kontrollera status för en uppdatering. Kommandona nedan visar status för den senaste uppdateringen med hjälp `default` av parametern. `-ApplyUpdateName` Du kan ersätta namnet på uppdateringen (returneras av kommandot [New-AzApplyUpdate)](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) för att få status för en viss uppdatering.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime kommer att vara den tidpunkt då uppdateringen blev klar, antingen initieras av dig eller av plattformen om självunderhåll fönster inte användes. Om det aldrig har varit en uppdatering som tillämpas via underhållskontroll kommer det att visa standardvärdet.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Sök efter uppdateringar av en viss virtuell dator.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Dedikerad värd

Sök efter uppdateringar till en dedikerad värd.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Ta bort en underhållskonfiguration

Använd [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) för att ta bort en underhållskonfiguration.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Underhåll och uppdateringar](maintenance-and-updates.md).
