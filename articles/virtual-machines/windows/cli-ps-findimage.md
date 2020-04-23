---
title: Välj Windows VM-avbildningar i Azure
description: Använd Azure PowerShell för att fastställa utgivare, erbjudande, SKU och version för Marketplace VM-avbildningar.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 46a2badbbe957f6a8a6af7f5a40633ea24cadcd4
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083373"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Hitta Windows VM-avbildningar på Azure Marketplace med Azure PowerShell

I den här artikeln beskrivs hur du använder Azure PowerShell för att hitta VM-avbildningar på Azure Marketplace. Du kan sedan ange en Marketplace-avbildning när du skapar en virtuell dator programmässigt med PowerShell, Resource Manager-mallar eller andra verktyg.

Du kan också bläddra bland tillgängliga avbildningar och erbjudanden med azure marketplace-skyltfönstret, [Azure-portalen](https://portal.azure.com)eller [Azure CLI](../linux/cli-ps-findimage.md). [Azure Marketplace](https://azuremarketplace.microsoft.com/) 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabell över vanliga Windows-avbildningar

I den här tabellen visas en delmängd av tillgängliga Skus för de angivna förlagen och erbjudandena.

| Utgivare | Erbjudande | Sku |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Kärna |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-med-behållare |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-med-behållare |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftSharePoint |MicrosoftSharePointServer |sp2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer (MicrosoftRServer) |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Navigera i bilderna

Ett sätt att hitta en bild på en plats är att köra Cmdlets [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)och [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) i ordning:

1. Visa en lista över avbildningsutgivare.
2. Visa en lista över erbjudanden från en viss utgivare.
3. Visa en lista över SKU:er för ett visst erbjudande.

Kör sedan [Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) för en vald SKU för att lista de versioner som ska distribueras.

1. Lista förlagen:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Fyll i ditt valda förlagsnamn och lista erbjudandena:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Fyll i ditt valda erbjudandenamn och lista SKU:erna:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Fyll i ditt valda SKU-namn och hämta bildversionen:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Från utdata `Get-AzVMImage` från kommandot kan du välja en versionsavbildning för att distribuera en ny virtuell dator.

I följande exempel visas hela sekvensen av kommandon och deras utdata:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
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

För *MicrosoftWindowsServer-utgivaren:*

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Resultat:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

För *WindowsServer-erbjudandet:*

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
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

Sedan, för *2019-Datacenter* SKU:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nu kan du kombinera den valda utgivaren, erbjudandet, SKU och versionen till ett URN (värden avgränsade med :). Skicka det här `--image` URN med parametern när du skapar en virtuell dator med cmdleten [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Du kan eventuellt ersätta versionsnumret i URN med "senaste" för att få den senaste versionen av bilden.

Om du distribuerar en virtuell dator med en Resource Manager-mall `imageReference` anger du bildparametrarna individuellt i egenskaperna. Se [mallreferensen](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Visa planegenskaper

Om du vill visa en bilds `Get-AzVMImage` inköpsplaninformation kör du cmdleten. Om `PurchasePlan` egenskapen i utdata inte `null`är det har avbildningen termer som du måste acceptera innan programmatisk distribution.  

Windows Server *2016* Datacenter-avbildningen har till exempel `PurchasePlan` inga `null`ytterligare villkor, så informationen är:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Resultat:

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

Exemplet nedan visar ett liknande kommando för avbildningen *Data Science Virtual Machine - Windows 2016,* `PurchasePlan` som har följande egenskaper: `name`, `product`och `publisher`. Vissa bilder har `promotion code` också en egenskap. Information om hur du distribuerar den här avbildningen finns i följande avsnitt för att acceptera villkoren och aktivera programmatisk distribution.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Resultat:

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

### <a name="accept-the-terms"></a>Acceptera villkoren

Om du vill visa licensvillkoren använder du cmdleten [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) och skickar in parametrarna för inköpsplan. Utdata ger en länk till villkoren för Marketplace-avbildningen och visar om du tidigare har godkänt villkoren. Var noga med att använda alla gemener i parametervärdena.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Resultat:

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

Använd cmdlet [set-azmarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) för att acceptera eller avvisa villkoren. Du behöver bara acceptera villkor en gång per prenumeration för avbildningen. Var noga med att använda alla gemener i parametervärdena. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Resultat:

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

### <a name="deploy-using-purchase-plan-parameters"></a>Distribuera med hjälp av parametrar för inköpsplan

När du har accepterat villkoren för en avbildning kan du distribuera en virtuell dator i den prenumerationen. Som visas i följande kodavsnitt använder du cmdlet [set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) för att ange marketplace-planinformation för VM-objektet. Ett komplett skript för att skapa nätverksinställningar för den virtuella datorn och slutföra distributionen finns i [exempel på PowerShell-skript .](powershell-samples.md)

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Du skickar sedan vm-konfigurationen tillsammans med `New-AzVM` nätverkskonfigurationsobjekt till cmdleten.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar `New-AzVM` en virtuell dator snabbt med cmdlet med hjälp av grundläggande avbildningsinformation finns i [Skapa en virtuell Windows-dator med PowerShell](quick-create-powershell.md).


Se ett PowerShell-skriptexempel för att [skapa en fullständigt konfigurerad virtuell dator](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


