---
title: Azure Hybrid-förmånen för Windows Server | Microsoft Docs
description: Lär dig att maximera din Windows Software Assurance-förmåner för att använda lokala licenser för Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: fef057b5d1e1ba8b03b04852376b1e5a49926008
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432413"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid-förmånen för Windows Server
För kunder med Software Assurance kan Azure Hybrid-förmånen för Windows Server du använda dina lokala Windows Server-licenser och köra Windows-datorer i Azure mot en lägre kostnad. Du kan använda Azure Hybrid-förmånen för Windows Server för att distribuera nya virtuella datorer med Windows OS. Den här artikeln går över anvisningar om hur du distribuerar nya virtuella datorer med Azure Hybrid-förmånen för Windows Server och hur du kan uppdatera befintliga köra virtuella datorer. Läs mer om Azure Hybrid-förmånen för Windows Server licensierings- och besparingar i den [Azure Hybrid-förmånen för Windows Server-licensiering sidan](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Varje licens för dubbla processorer eller varje uppsättning av licenser för processorer med 16 kärnor berättigar innehavaren till två instanser med upp till åtta processorkärnor eller en instans med upp till 16 processorkärnor. Azure Hybrid-användningsförmånen för Standard Edition-licenser kan endast användas en gång – antingen lokalt eller i Azure. Datacenter Edition-förmånerna berättigar till samtidig användning både lokalt och i Azure.
>

> [!Important]
> Med Azure Hybrid-förmånen för Windows Server med alla virtuella datorer som kör Windows Server-Operativsystemet stöds nu i alla regioner, inklusive virtuella datorer med ytterligare programvara som SQL Server eller från tredje part marketplace-programvara. 
>

> [!NOTE]
> För klassiska virtuella datorer stöds endast distribuera nya virtuella datorn från en lokal anpassade avbildningar. Om du vill dra nytta av funktionerna som stöds i den här artikeln, måste du först migrera klassiska virtuella datorer till Resource Manager-modellen.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Sätt att använda Azure Hybrid-förmånen för Windows Server
Det finns flera sätt att använda Windows-datorer med Azure Hybrid-förmånen:

1. Du kan distribuera virtuella datorer från en av de angivna [Windows Server-avbildningar på Azure Marketplace](# https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Du kan ladda upp en anpassad virtuell dator och distribuera med hjälp av en Resource Manager-mall eller Azure PowerShell
3. Du kan växla mellan och konvertera befintlig virtuell dator mellan att köra med Azure Hybrid-förmånen eller betala på begäran-kostnaden för Windows Server
4. Du kan också använda Azure Hybrid-förmånen för Windows Server på VM-skalningsuppsättning samt


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Skapa en virtuell dator med Azure Hybrid-förmånen för Windows Server
Alla Windows Server-operativsystem baserade avbildningar har stöd för Azure Hybrid-förmånen för Windows Server. Du kan använda Azure-plattformen support-avbildningar eller ladda upp dina egna anpassade avbildningar för Windows Server. 

### <a name="portal"></a>Portalen
Använd växlingsknappen för att skapa en virtuell dator med Azure Hybrid-förmånen för Windows Server, under avsnittet ”Spara pengar”.

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Mall
I Resource Manager-mallar, en extra parameter `licenseType` måste anges. Du kan läsa mer om [skapa Azure Resource Manager-mallar](../../resource-group-authoring-templates.md)
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konvertera en befintlig virtuell dator med hjälp av Azure Hybrid-förmånen för Windows Server
Om du har en befintlig virtuell dator som du vill konvertera om du vill utnyttja Azure Hybrid-förmånen för Windows Server kan uppdatera du licenstyp för den virtuella datorn genom att följa anvisningarna nedan.

> [!NOTE]
> Ändra licenstypen på den virtuella datorn orsakar inte systemet att starta om eller orsaka en interuption för tjänsten.  Det är bara en uppdatering av en metadataflaggan.
> 

### <a name="portal"></a>Portalen
Från bladet VM-portalen kan du uppdatera den virtuella datorn för att använda Azure Hybrid-förmånen genom att välja alternativet ”Configuration” och växla mellan alternativet ”Azure hybrid-förmån”

### <a name="powershell"></a>PowerShell
- Konvertera befintliga Windows Server-datorer till Azure Hybrid-förmånen för Windows Server

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Konvertera virtuella Windows Server-datorer med förmånen tillbaka till betala per användning

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Konvertera befintliga Windows Server-datorer till Azure Hybrid-förmånen för Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Så här verifierar du den virtuella datorn använder licensieringsförmånen
När du har distribuerat den virtuella datorn via antingen PowerShell Resource Manager-mall eller portal kan du kontrollera inställningen i följande metoder.

### <a name="portal"></a>Portalen
Från bladet VM-portalen kan du visa växlingsknappen för Azure Hybrid-förmånen för Windows Server genom att välja fliken ”konfiguration”.

### <a name="powershell"></a>PowerShell
I följande exempel visas licenstypen för en enskild virtuell dator
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Resultat:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Detta utdata visas med följande VM distribuerats utan Azure Hybrid-förmånen för Windows Server-licens:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query '[?licenseType==Windows_Server]' -o table
```

> [!NOTE]
> Ändra licenstypen på den virtuella datorn orsakar inte systemet att starta om eller orsaka en interuption för tjänsten. Det är en licensiering flaggan endast metadata.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Lista över alla virtuella datorer med Azure Hybrid-förmånen för Windows Server i en prenumeration
Du kan köra följande kommando från din prenumeration för att se och räkna alla virtuella datorer som distribueras med Azure Hybrid-förmånen för Windows Server:

### <a name="portal"></a>Portalen
Från virtuell dator eller virtuell dator scale sets resursbladet, kan du visa en lista över alla dina virtuella datorer och licensiering typ genom att konfigurera kolumnen om du vill inkludera ”Azure Hybrid-förmånen”. VM-inställningen kan antingen vara i ”Enabled”, tillstånd ”inte aktiverad” eller ”stöds inte”.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzureRMVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query '[?licenseType==Windows_Server]' -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Distribuera en VM-Skalningsuppsättning med Azure Hybrid-förmånen för Windows Server
I din VM scale Sets Resource Manager-mallar, en extra parameter `licenseType` måste anges inom VirtualMachineProfile-egenskapen. Du kan göra det under Skapa eller uppdatera för din skalningsuppsättning med ARM-mallen, Powershell, Azure CLI eller REST.

I följande exempel använder ARM-mall med en Windows Server 2016 Datacenter-avbildning:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Du kan också läsa mer om hur du [ändra en VM-skalningsuppsättning](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) för fler sätt att uppdatera din skalningsuppsättning.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [hur du kan spara pengar med Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Läs mer om [vanliga frågor om Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Läs mer om [Azure Hybrid-förmånen för Windows Server-licensiering detaljerad vägledning](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Läs mer om [Azure Hybrid-förmånen för Windows Server och Azure Site Recovery göra migrera program till Azure ännu mer kostnadseffektivt](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Läs mer om [Windows 10 på Azure med Multitenant som är värd för höger](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Läs mer om [med hjälp av Resource Manager-mallar](../../azure-resource-manager/resource-group-overview.md)
