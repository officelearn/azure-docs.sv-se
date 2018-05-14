---
title: Hybridrapportering i Azure-förmån för Windows Server | Microsoft Docs
description: Lär dig att maximera dina Windows Software Assurance-förmåner för att försätta lokalt licenser i Azure
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
ms.openlocfilehash: a4b0baefc8c3c839a06d6540e57b34657138c8ff
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid-förmånen för Windows Server
För kunder med Software Assurance kan Azure Hybrid-förmån för Windows Server du använda dina lokala Windows Server-licenser och köra virtuella Windows-datorer i Azure till en lägre kostnad. Du kan använda Azure Hybrid-förmån för Windows Server för att distribuera nya virtuella datorer med Windows-Operativsystemet. Den här artikeln går över stegen på hur du distribuerar nya virtuella datorer med Azure Hybrid-förmån för Windows Server och hur du kan uppdatera befintliga virtuella datorer som körs. Mer information om Azure Hybrid-förmån för Windows Server finns i licens- och besparingar i [Azure Hybrid-förmån för Windows Server-licensiering sidan](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Varje licens för dubbla processorer eller varje uppsättning av licenser för processorer med 16 kärnor berättigar innehavaren till två instanser med upp till åtta processorkärnor eller en instans med upp till 16 processorkärnor. Azure Hybrid-användningsförmånen för Standard Edition-licenser kan endast användas en gång – antingen lokalt eller i Azure. Datacenter Edition-förmånerna berättigar till samtidig användning både lokalt och i Azure.
>

> [!Important]
> Med alla virtuella datorer som kör Windows Server-OS Azure Hybrid-förmån för Windows Server stöds nu i alla regioner, inklusive virtuella datorer med ytterligare program som SQL Server eller en tredje parts marketplace. 
>

> [!NOTE]
> För klassiska virtuella datorer kan stöds endast distribuera nya virtuella datorn från lokala anpassade avbildningar. Om du vill dra nytta av funktioner som stöds i den här artikeln, måste du migrera klassiska virtuella datorer till Resource Manager-modellen.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Sätt att använda Azure Hybrid-förmån för Windows Server
Det finns några olika sätt att använda Windows-datorer med förmån för Azure-Hybrid:

1. Du kan distribuera virtuella datorer från en av de angivna [Windows Server-avbildningar på Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Du kan ladda upp en egen virtuell dator och distribuera med hjälp av en Resource Manager-mall eller Azure PowerShell
3. Du kan växla och konvertera befintlig virtuell dator mellan körs med Azure Hybrid förmån eller betala på begäran kostnaden för Windows Server
4. Du kan också använda Azure Hybrid-förmån för Windows Server på virtuella skaluppsättningen samt


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Skapa en virtuell dator med Hybridrapportering i Azure-förmån för Windows Server
Alla Windows Server baserad operativsystemsavbildningar har stöd för Azure Hybrid-förmån för Windows Server. Du kan använda Azure-plattformen stöd bilder eller överföra din egen anpassade Windows Server-avbildningar. 

### <a name="portal"></a>Portalen
Använd växeln under avsnittet ”Spara pengar” om du vill skapa en virtuell dator med Azure Hybrid-förmån för Windows Server.

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
I Resource Manager-mallar, en extra parameter `licenseType` måste anges. Du kan läsa mer om [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md)
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konvertera en befintlig virtuell dator med hjälp av Azure Hybrid-förmån för Windows Server
Om du har en befintlig virtuell dator som du vill konvertera om du vill dra nytta av Azure Hybrid-förmån för Windows Server kan du uppdatera den Virtuella datorns licenstypen enligt följande:

### <a name="portal"></a>Portalen
Från portalen VM-bladet, kan du uppdatera den virtuella datorn om du vill använda Azure Hybrid dra genom att välja alternativet ”Configuration” och växla mellan alternativet ”Azure hybrid dra”

### <a name="powershell"></a>PowerShell
- Konvertera befintliga Windows Server-datorer till Azure Hybrid-förmån för Windows Server

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Konvertera virtuella Windows Server-datorer med förmån tillbaka till betala per användning

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Konvertera befintliga Windows Server-datorer till Azure Hybrid-förmån för Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```
    
### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Så här kontrollerar du den virtuella datorn använder licensiering fördelen
När du har distribuerat den virtuella datorn med hjälp av antingen PowerShell Resource Manager-mall eller portal, kan du kontrollera inställningen i följande metoder.

### <a name="portal"></a>Portalen
Från portalen VM-bladet, kan du visa att växlingsknappen för Azure Hybrid-förmån för Windows Server genom att välja fliken ”konfiguration”.

### <a name="powershell"></a>PowerShell
I följande exempel visas licenstypen för en enda virtuell dator
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Resultat:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Detta utdata skiljer från med följande VM distribueras utan Azure Hybrid-förmån för Windows Server-licensiering:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query '[?licenseType==Windows_Server]' -o table
```

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Lista alla virtuella datorer med Azure Hybrid-förmån för Windows Server i en prenumeration
Du kan köra följande kommando från prenumerationen för att se och räkna alla virtuella datorer som distribueras med Azure Hybrid-förmån för Windows Server:

### <a name="portal"></a>Portalen
Du kan se en lista över alla virtuella datorer och licensiering typ genom att konfigurera kolumnen om du vill inkludera ”Azure Hybrid förmånen” virtuell dator eller virtuell dator skala anger resursbladet. Inställningen för virtuell dator kan antingen vara i ”aktiverad”, tillstånd ”inte aktiverad” eller ”stöds inte”.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzureRMVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query '[?licenseType==Windows_Server]' -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Distribuera en Skaluppsättning för virtuell dator med Hybridrapportering i Azure-förmån för Windows Server
I den virtuella datorn skaluppsättning för Resource Manager-mallar, en extra parameter `licenseType` måste anges i VirtualMachineProfile-egenskap. Du kan göra detta under Skapa eller uppdatera för nivå anges via ARM-mallen, Powershell, Azure CLI eller REST.

I följande exempel används ARM-mallen med en Windows Server 2016 Datacenter-avbildning:
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
Du kan också lära dig mer om hur du [ändra en virtuella datorns skaluppsättning](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) fler sätt att uppdatera din skala anges.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [så spara pengar med förmån för Azure-Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Läs mer om [vanliga frågor om Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Lär dig mer om [Azure Hybrid-förmån för Windows Server-licensiering detaljerad vägledning](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Lär dig mer om [Azure Hybrid-förmån för Windows Server och Azure Site Recovery göra migrera program till Azure ännu mer kostnadseffektivt](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Lär dig mer om [Windows 10 på Azure med Multitenant värd höger](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Lär dig mer om [med hjälp av Resource Manager-mallar](../../azure-resource-manager/resource-group-overview.md)
