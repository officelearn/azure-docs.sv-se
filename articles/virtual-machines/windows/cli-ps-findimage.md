---
title: Välj Windows VM-avbildningar i Azure | Microsoft Docs
description: Använda Azure PowerShell för att fastställa utgivare, erbjudande, SKU och version för Marketplace-VM-avbildningar.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: danlep
ms.openlocfilehash: 7a300826db512a813282eea71d2e898f0221a977
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077751"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Hitta Windows VM-avbildningar på Azure Marketplace med Azure PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att hitta VM-avbildningar på Azure Marketplace. Du kan sedan ange en Marketplace-avbildning när du skapar en virtuell dator via programmering med PowerShell, Resource Manager-mallar eller andra verktyg.

Du kan också gå tillgängliga avbildningar och erbjudanden med hjälp av den [Azure Marketplace](https://azuremarketplace.microsoft.com/) storefront, den [Azure-portalen](https://portal.azure.com), eller [Azure CLI](../linux/cli-ps-findimage.md). 

Se till att du har installerat och konfigurerat senast [Azure PowerShell-modulen](/powershell/azure/azurerm/install-azurerm-ps).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabell med vanliga Windows-avbildningar

Den här tabellen visar en delmängd av tillgängliga SKU: er för angivna utgivare och erbjudanden.

| Utgivare | Erbjudande | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019 Datacenter med behållare |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 Datacenter med behållare |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Navigera bland avbildningarna

Ett sätt att hitta en bild på en plats är att köra den [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), och [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) cmdletar i ordning:

1. Visa en lista över avbildningsutgivare.
2. Visa en lista över erbjudanden från en viss utgivare.
3. Visa en lista över SKU:er för ett visst erbjudande.

Kör sedan följande för en vald SKU [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) listan över versioner för att distribuera.

1. Lista över utgivare:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Fyll i ditt valda Utgivarnamn och lista erbjudanden:

    ```powershell
    $pubName="<publisher>"
    Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
    ```

3. Fyll i namnet på din valda erbjudandet och lista över SKU: er:

    ```powershell
    $offerName="<offer>"
    Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
    ```

4. Fyll i din valda SKU-namnet och hämta versionsnumret för avbildningen:

    ```powershell
    $skuName="<SKU>"
    Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
    ```

Från utdata från den `Get-AzureRMVMImage` kommandot, kan du välja en version bild för att distribuera en ny virtuell dator.

I följande exempel visas kontosystem kommandon och deras utdata:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Delvisa utdata:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

För den *MicrosoftWindowsServer* utgivare:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Utdata:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

För den *WindowsServer* erbjuder:

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Delvisa utdata:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Sedan för den *2019 Datacenter* SKU:

```powershell
$skuName="2019-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nu kan du kombinera valda utgivare, erbjudande, SKU och version till en URN (värden separerade med:). Skicka den här URN med den `--image` parameter när du skapar en virtuell dator med den [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet. Du kan också ersätta versionsnumret i URN med ”senaste” för att få den senaste versionen av avbildningen.

Om du distribuerar en virtuell dator med Resource Manager-mall så du måste ange parametrarna bild individuellt i den `imageReference` egenskaper. Se [mallreferensen](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Visa egenskaperna för energischemat

Om du vill visa en bild Köpinformation för plan, kör den `Get-AzureRMVMImage` cmdlet. Om den `PurchasePlan` -egenskapen i utdata är inte `null`, avbildningen har villkoren måste du godkänna innan programdistribution.  

Till exempel den *Windows Server 2016 Datacenter* bilden inte har ytterligare villkor så `PurchasePlan` information är `null`:

```powershell
$version = "2019.0.20190115"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
```

Utdata:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Exemplet nedan visar ett liknande kommando för den *virtuella dator för datavetenskap – Windows 2016* avbildningen, som har följande `PurchasePlan` egenskaper: `name`, `product`, och `publisher`. Vissa bilder har också en `promotion code` egenskapen. Finns i följande avsnitt att acceptera villkoren och aktivera programdistribution för att distribuera den här avbildningen.

```powershell
Get-AzureRMVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "19.01.14"
```

Utdata:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Godkänn villkoren

Du kan visa licensvillkoren på [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) cmdlet och lägger köpet planera parametrar. Utdata innehåller en länk till villkoren för Marketplace-avbildning och visar om du tidigare har accepterat villkoren. Glöm inte att använda gemener i parametrarnas värden.

```powershell
Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Utdata:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Använd den [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) cmdlet för att godkänna eller avvisa villkoren. Du behöver bara godkänna villkoren en gång per prenumeration för avbildningen. Glöm inte att använda gemener i parametrarnas värden. 

```powershell
$agreementTerms=Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzureRmMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Utdata:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Distribuera med hjälp av köp plan parametrar

Du kan distribuera en virtuell dator i den aktuella prenumerationen efter att du godkänt villkoren för en bild. Som du ser i följande kodavsnitt kan använda den [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) cmdleten Marketplace-informationen för det Virtuella datorobjektet för planen. En fullständig skript för att skapa nätverksinställningar för den virtuella datorn och slutföra distributionen, finns det [PowerShell-exempelskript](powershell-samples.md).

```powershell
...

$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Du kommer sedan skickar VM-konfigurationen tillsammans med network configuration-objekt till den `New-AzureRmVM` cmdlet.

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator snabbt med den `New-AzureRmVM` cmdlet med basic-avbildningen information, se [skapa en Windows-dator med PowerShell](quick-create-powershell.md).

Se ett exempel på PowerShell-skript till [skapa en fullständigt konfigurerad virtuell dator](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


