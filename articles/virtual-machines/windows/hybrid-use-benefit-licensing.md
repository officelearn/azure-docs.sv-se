---
title: "Hybridrapportering i Azure-förmån för Windows Server | Microsoft Docs"
description: "Lär dig att maximera dina Windows Software Assurance-förmåner för att försätta lokalt licenser i Azure"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss
ms.openlocfilehash: c2b406530aec60299ea2db38ad9e34895fe36dcd
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid-förmånen för Windows Server
För kunder med Software Assurance kan Azure Hybrid-förmån för Windows Server du använda dina lokala Windows Server-licenser och köra virtuella Windows-datorer i Azure till en lägre kostnad. Du kan använda Azure Hybrid-förmån för Windows Server för att distribuera nya virtuella datorer från eventuella Azure stöds Windows Server plattformsavbildning eller anpassade Windows-avbildningar. Den här artikeln går över stegen på hur du distribuerar nya virtuella datorer med Azure Hybrid-förmån för Windows Server och hur du kan uppdatera befintliga virtuella datorer som körs. Mer information om Azure Hybrid-förmån för Windows Server finns i licens- och besparingar i [Azure Hybrid-förmån för Windows Server-licensiering sidan](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> De äldre [hubb] Windows Server-avbildningar som har publicerats för kunder med Enterprise-avtal på Azure Marketplace har tagits bort från och med 9/11/2017 använder standard Windows Server med alternativet ”Spara pengar” på portalen för Azure Hybrid-förmån för Windows Server. Mer information hittar du detta [artikel.](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions)
>

> [!NOTE]
> Med hjälp av Azure Hybrid-förmån för Windows Server med virtuella datorer som debiteras för ytterligare programvara, till exempel SQL Server eller någon tredje parts marketplace-bilder som lyfts. Om du får felet 409 exempel: ändra egenskapen 'LicenseType' inte är tillåtet; sedan försöker du konvertera eller distribuera en ny Windows virtuell dator som har ytterligare programvara kostnad, vilket inte stöds i den regionen. Samma om du försöker leta för portalen konfigurationen alternativet för konvertering och du kan inte se den för den virtuella datorn.
>


> [!NOTE]
> För klassiska virtuella datorer kan stöds endast distribuera nya virtuella datorn från lokala anpassade avbildningar. Om du vill dra nytta av funktioner som stöds i den här artikeln, måste du migrera klassiska virtuella datorer till Resource Manager-modellen.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Sätt att använda Azure Hybrid-förmån för Windows Server
Det finns några olika sätt att använda Windows-datorer med förmån för Azure-Hybrid:

1. Du kan distribuera virtuella datorer från en av de angivna [Windows Server-avbildningar på Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Du kan [ladda upp en anpassad VM](#upload-a-windows-vhd) och [distribueras med hjälp av en Resource Manager-mall](#deploy-a-vm-via-resource-manager) eller [Azure PowerShell](#detailed-powershell-deployment-walkthrough)
3. Du kan växla och konvertera befintlig virtuell dator mellan körs med Azure Hybrid förmån eller betala på begäran kostnaden för Windows Server
4. Du kan också distribuera en ny virtuell dator-skala med Azure Hybrid-förmån för Windows Server

> [!NOTE]
> Konvertera en befintlig virtuell dator skala inställd på att använda Azure Hybrid-förmån för Windows Server stöds inte
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Distribuera en virtuell dator från en Windows Server Marketplace-avbildning
Alla Windows Server-avbildningar som är tillgängliga från Azure Marketplace är aktiverade med Azure Hybrid-förmån för Windows Server. Till exempel Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 och Windows Server 2008SP1 och mycket mer. Du kan använda dessa avbildningar för att distribuera virtuella datorer direkt från Azure-portalen, Resource Manager-mallar, Azure PowerShell eller andra SDK: er.

Du kan distribuera dessa avbildningar direkt från Azure-portalen. För användning i Resource Manager-mallar med Azure PowerShell, visas en lista med bilder på följande sätt:

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
Du kan följa stegen för att [skapar en virtuell Windows-dator med PowerShell](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) och skicka LicenseType = ”Windows_Server”. Det här alternativet kan du använda din befintliga Windows Server-licens i Azure.

### <a name="portal"></a>Portalen
Du kan följa stegen för att [skapa en Windows-dator med Azure-portalen](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) och väljer alternativet att använda din befintliga Windows Server-licens.

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konvertera en befintlig virtuell dator med hjälp av Azure Hybrid-förmån för Windows Server
Om du har en befintlig virtuell dator som du vill konvertera om du vill dra nytta av Azure Hybrid-förmån för Windows Server kan du uppdatera den Virtuella datorns licenstypen enligt följande:

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>Omvandla till med hjälp av Azure Hybrid-förmån för Windows Server
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>Omvandla tillbaka för att betala allteftersom
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>Portalen
Från portalen VM-bladet, kan du uppdatera den virtuella datorn om du vill använda Azure Hybrid dra genom att välja alternativet ”Configuration” och växla mellan alternativet ”Azure hybrid dra”

> [!NOTE]
> Om du inte ser alternativet att växla ”Azure hybrid förmånen” under ”Configuration” är det eftersom konverteringen inte stöds ännu för den valda virtuella datorn (till exempel en virtuell dator inbyggda från anpassad avbildning eller från en avbildning som har ytterligare betald program som SQL Server eller Azure Marketplace tredjepartsprogram).
>

## <a name="upload-a-windows-server-vhd"></a>Överför en Windows Server VHD
Om du vill distribuera en Windows Server-VM i Azure måste du först skapa en virtuell Hårddisk som innehåller din grundläggande Windows-version. Den här virtuella Hårddisken måste förberedas på rätt sätt via Sysprep innan du överför den till Azure. Du kan [Läs mer om kraven för virtuell Hårddisk och Sysprep-processen](upload-generalized-managed.md) och [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Säkerhetskopiera den virtuella datorn innan du kör Sysprep. 

När du har förberett den virtuella Hårddisken, överför den virtuella Hårddisken till Azure Storage-konto på följande sätt:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server och Dynamics kan också använda din Software Assurance-licensiering. Skall måste du förbereda Windows Server-avbildning genom att installera programkomponenterna och tillhandahåller licensnycklar i enlighet med detta och sedan ladda upp disk image till Azure. Dokumentationen från lämplig kör Sysprep med ditt program, till exempel [överväganden för installation av SQL Server med hjälp av Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) eller [skapa SharePoint Server 2016 referensbild (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).
>
>

Du kan också läsa mer om [överför den virtuella Hårddisken till Azure-processen](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)

## <a name="deploy-a-vm-via-resource-manager-template"></a>Distribuera en virtuell dator via Resource Manager-mall
I Resource Manager-mallar, en extra parameter `licenseType` måste anges. Du kan läsa mer om [skapa mallar för Azure Resource Manager](../../resource-group-authoring-templates.md). När du har den virtuella Hårddisken överförs till Azure kan redigera Resource Manager-mall om du vill inkludera licenstypen som en del av compute-providern och distribuera mallen som vanligt:

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Distribuera en virtuell dator via PowerShell-Snabbstart
När du distribuerar Windows Server-VM via PowerShell kan du ha en parameter av ytterligare `-LicenseType`. När du har den virtuella Hårddisken överförs till Azure kan du skapa en virtuell dator med hjälp av `New-AzureRmVM` och ange licensiering på följande sätt:

För Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Du kan läsa en mer beskrivande guide på olika steg för att [skapa en virtuell Windows-dator med Resource Manager och PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Kontrollera den virtuella datorn använder licensiering fördelen
När du har distribuerat den virtuella datorn med hjälp av antingen PowerShell Resource Manager-mall eller portal, kan du kontrollera licenstypen med `Get-AzureRmVM` på följande sätt:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Utdata liknar följande exempel för Windows Server:

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

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>Visa en lista med alla Azure Hybrid-förmån för Windows Server-datorer i en prenumeration

Du kan köra följande kommando från prenumerationen för att se och räkna alla virtuella datorer som distribueras med Azure Hybrid-förmån för Windows Server:

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Distribuera en virtuell dator-skala med Azure Hybrid-förmån för Windows Server
I den virtuella datorn skaluppsättning för Resource Manager-mallar, en extra parameter `licenseType` måste anges. Du kan läsa mer om [skapa mallar för Azure Resource Manager](../../resource-group-authoring-templates.md). Redigera Resource Manager-mall om du vill inkludera egenskapen licenseType som en del av den skaluppsättning virtualMachineProfile och distribuera mallen som vanligt - Se följande exempel på användning av Windows Server 2016-avbildning:


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
Du kan också [skapa och distribuera en virtuella datorns skaluppsättning](#https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) och egenskapen LicenseType

## <a name="next-steps"></a>Nästa steg
Läs mer om [så spara pengar med förmån för Azure-Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

Lär dig mer om [Azure Hybrid-förmån för Windows Server-licensiering detaljerad vägledning](https://docs.microsoft.com/en-us/windows-server/get-started/azure-hybrid-benefit)

Lär dig mer om [med hjälp av Resource Manager-mallar](../../azure-resource-manager/resource-group-overview.md)

Lär dig mer om [Azure Hybrid-förmån för Windows Server och Azure Site Recovery göra migrera program till Azure ännu mer kostnadseffektivt](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)

Lär dig mer om [Windows 10 på Azure med Multitenant värd höger](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)

Läs mer om [vanliga frågor och svar](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/)
