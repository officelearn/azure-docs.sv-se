---
title: Azure Hybrid-förmånen för Windows Server
description: Lär dig hur du maximerar fördelarna med Windows Software Assurance för att få lokala licenser till Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: 470e38c21a250273216f93eb38a5334a4bb581e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911803"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid-förmånen för Windows Server
För kunder med Software Assurance kan du med Azure Hybrid Benefit för Windows Server använda dina lokala Windows Server-licenser och köra virtuella Windows-datorer på Azure till en lägre kostnad. Du kan använda Azure Hybrid Benefit för Windows Server för att distribuera nya virtuella datorer med Windows OS. Den här artikeln går igenom stegen för hur du distribuerar nya virtuella datorer med Azure Hybrid Benefit för Windows Server och hur du kan uppdatera befintliga virtuella datorer som körs. Mer information om Azure Hybrid Benefit för Windows Server-licensiering och kostnadsbesparingar finns på [licensieringssidan för Azure Hybrid Benefit för Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Varje licens med två processorer eller varje uppsättning 16 kärnlicenser har rätt till två instanser på upp till 8 kärnor, eller en instans på upp till 16 kärnor. Azure Hybrid-förmånen för Standard Edition-licenser kan bara användas en gång antingen lokalt eller i Azure. Datacenter Edition-förmåner möjliggör samtidig användning både lokalt och i Azure.

Att använda Azure Hybrid Benefit för Windows Server med virtuella datorer som kör Windows Server OS stöds nu i alla regioner, inklusive virtuella datorer med ytterligare programvara som SQL Server eller marknadsplatsprogramvara från tredje part. 


## <a name="classic-vms"></a>Klassiska virtuella datorer

För klassiska virtuella datorer stöds endast distribution av ny virtuell dator från lokala anpassade avbildningar. Om du vill dra nytta av de funktioner som stöds i den här artikeln måste du först migrera klassiska virtuella datorer till Resource Manager-modellen.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Olika sätt att använda Azure Hybrid Benefit för Windows Server
Det finns några sätt att använda virtuella Windows-datorer med Azure Hybrid Benefit:

1. Du kan distribuera virtuella datorer från en av de medföljande Windows Server-avbildningarna på Azure Marketplace
2. Du kan ladda upp en anpassad virtuell dator och distribuera med hjälp av en Resource Manager-mall eller Azure PowerShell
3. Du kan växla och konvertera befintlig virtuell dator mellan att köra med Azure Hybrid Benefit eller betala kostnad på begäran för Windows Server
4. Du kan också använda Azure Hybrid Benefit för Windows Server på skalningsuppsättning för virtuella datorer


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Skapa en virtuell dator med Azure Hybrid Benefit för Windows Server
Alla Windows Server OS-baserade avbildningar stöds för Azure Hybrid Benefit för Windows Server. Du kan använda Azure-plattformsstödavbildningar eller ladda upp egna anpassade Windows Server-avbildningar. 

### <a name="portal"></a>Portalen
Om du vill skapa en virtuell dator med Azure Hybrid Benefit för Windows Server använder du växlingsknappen under avsnittet "Spara pengar".

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
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
I Resource Manager-mallarna måste `licenseType` ytterligare en parameter anges. Du kan läsa mer om [hur du skapar Azure Resource Manager-mallar](../../resource-group-authoring-templates.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konvertera en befintlig virtuell dator med Azure Hybrid Benefit för Windows Server
Om du har en befintlig virtuell dator som du vill konvertera för att dra nytta av Azure Hybrid Benefit för Windows Server kan du uppdatera den virtuella datorns licenstyp genom att följa instruktionerna nedan.

> [!NOTE]
> Om du ändrar licenstypen på den virtuella datorn kan systemet inte startas om eller orsaka en tjänst interuption.  Det är helt enkelt en uppdatering av en metadataflagga.
> 

### <a name="portal"></a>Portalen
Från bladet virtuell dator med portal kan du uppdatera den virtuella datorn för att använda Azure Hybrid Benefit genom att välja alternativet "Konfiguration" och växla alternativet "Azure hybrid benefit"

### <a name="powershell"></a>PowerShell
- Konvertera befintliga virtuella Windows Server-datorer till Azure Hybrid Benefit för Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Konvertera virtuella windows server-datorer med förmån tillbaka till betalning per du kör

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Konvertera befintliga virtuella Windows Server-datorer till Azure Hybrid Benefit för Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Så här verifierar du att den virtuella datorn använder licensförmånen
När du har distribuerat den virtuella datorn via antingen PowerShell, Resource Manager-mall eller portal kan du verifiera inställningen på följande metoder.

### <a name="portal"></a>Portalen
Från bladet Virtuell portal kan du visa växlingsknappen för Azure Hybrid Benefit för Windows Server genom att välja fliken Konfiguration.

### <a name="powershell"></a>PowerShell
I följande exempel visas licenstypen för en enda virtuell dator
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Resultat:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Den här utdata står i kontrast till följande virtuella dator som distribueras utan Azure Hybrid Benefit för Windows Server-licensiering:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Om du ändrar licenstypen på den virtuella datorn kan systemet inte startas om eller orsaka en tjänst interuption. Det är endast en metadatalicensieringsflagga.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Lista alla virtuella datorer med Azure Hybrid Benefit för Windows Server i en prenumeration
Om du vill visa och räkna alla virtuella datorer som distribueras med Azure Hybrid Benefit för Windows Server kan du köra följande kommando från din prenumeration:

### <a name="portal"></a>Portalen
Från den virtuella datorn eller skalan för virtuella datorer anger resursblad kan du visa en lista över alla dina virtuella datorer och licensieringstyp genom att konfigurera tabellkolumnen så att den inkluderar "Azure Hybrid Benefit". Vm-inställningen kan antingen vara i tillståndet "Aktiverad", "Inte aktiverad" eller "Stöds inte".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Distribuera en skalningsuppsättning för virtuella datorer med Azure Hybrid-förmån för Windows Server
I resursstyrningsmallarna för virtuell datoruppsättning `licenseType` måste ytterligare en parameter anges i egenskapen VirtualMachineProfile. Du kan göra detta under att skapa eller uppdatera för din skalningsuppsättning via ARM-mallen, PowerShell, Azure CLI eller REST.

I följande exempel används ARM-mall med en Datacenteravbildning för Windows Server 2016:
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
Du kan också läsa mer om hur du [ändrar en skalningsuppsättning](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) för virtuella datorer för fler sätt att uppdatera skalningsuppsättningen.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [hur du sparar pengar med Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Läs mer om [Vanliga frågor och svar för Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Läs mer om [detaljerad vägledning för Azure Hybrid Benefit för Windows Server](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Läs mer om [Azure Hybrid Benefit för Windows Server och Azure Site Recovery gör migreringsprogram till Azure ännu mer kostnadseffektiva](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Läs mer om [Windows 10 på Azure med tjänsten för flera nde värdar](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Läs mer om [Hur du använder Resource Manager-mallar](../../azure-resource-manager/management/overview.md)
