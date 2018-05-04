---
title: Välj Windows VM-avbildningar i Azure | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell för att fastställa utgivare, erbjudande, SKU och version för Marketplace VM-avbildningar.
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
ms.date: 02/28/2018
ms.author: danlep
ms.openlocfilehash: 885ee10bc63b65d936f5b433a18c4435b2503720
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Hitta Windows VM-avbildningar i Azure Marketplace med Azure PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att hitta VM-avbildningar i Azure Marketplace. Använd informationen för att ange en Marketplace-avbildning när du skapar en virtuell dator via programmering med PowerShell Resource Manager-mallar eller andra verktyg.

Kontrollera att du har installerat och konfigurerat senast [Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabell med vanliga Windows-avbildningar
| Utgivare | Erbjudande | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016 Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 Datacenter med behållare |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="navigate-the-images"></a>Navigera avbildningar

Ett annat sätt att hitta en bild på en plats är att köra den [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), och [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) cmdlets i följd. Med dessa kommandon bestämma dessa värden:

1. Visa en lista över avbildningsutgivare.
2. Visa en lista över erbjudanden från en viss utgivare.
3. Visa en lista över SKU:er för ett visst erbjudande.

Kör sedan följande för en markerad SKU [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) att visa versioner för att distribuera.

Börja med att skapa en lista över utgivare med hjälp av följande kommandon:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Fyll i namnet på den valda utgivaren och kör följande kommandon:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Fyll i namnet på det valda erbjudandet och kör följande kommandon:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Fyll i ditt valda SKU namn och kör följande kommandon:

```powershell
$skuName="<SKU>"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Från utdata från den `Get-AzureRMVMImage` kommandot kan du välja en version bild för att distribuera en ny virtuell dator.

Följande kommandon visar ett fullständigt exempel:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Resultat:

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

För den *Microsoft Windows Server* publisher:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Resultat:

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

Resultat:

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
2016-Nano-Server
```

Sedan för den *2016 Datacenter* SKU:

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nu kan du kombinera valda utgivare, erbjudande, SKU och version till en URN (kommatecken:). Skicka den här URN med den `--image` parameter när du skapar en virtuell dator med den [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet. Kom ihåg att du kan ersätta versionsnumret i URN med ”senaste”. Den här versionen är alltid den senaste versionen av avbildningen. Du kan också använda URN med den [Set AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) PowerShell-cmdlet. 

Om du distribuerar en virtuell dator med en Resource Manager-mall måste du ange parametrarna bild individuellt i den `imageReference` egenskaper. Finns det [mallreferensen](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]


### <a name="view-plan-properties"></a>Visa plan egenskaper

Om du vill visa information om en avbildning köp plan kör den `Get-AzureRMVMImage` cmdlet. Om den `PurchasePlan` -egenskapen i utdata är inte `null`, avbildningen har villkoren måste du acceptera för programdistribution.  

Till exempel Windows Server 2016 Datacenter bilden inte har ytterligare villkor, eftersom den `PurchasePlan` information `null`:

```powershell
$version = "2016.127.20170406"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
```

Resultat:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/
                   Versions/2016.127.20170406
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2016-Datacenter
Version          : 2016.127.20170406
FilterExpression :
Name             : 2016.127.20170406
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Kör ett liknande kommando för den datavetenskap virtuella - Windows 2016 bilden visar följande `PurchasePlan` egenskaper: `name`, `product`, och `publisher`. (Vissa bilder har också en `promotion code` egenskap.) Om du vill distribuera den här avbildningen finns i följande avsnitt att acceptera villkoren och aktivera programdistribution.

```powershell
Get-AzureRMVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Resultat:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMIma
                   ge/Offers/windows-data-science-vm/Skus/windows2016/Versions/0.2.02
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 0.2.02
FilterExpression :
Name             : 0.2.02
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

Om du vill visa licensvillkoren, Använd den [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) cmdlet- och pass i köpet planera parametrar. Utdata innehåller en länk till villkoren för Marketplace-avbildning och visar om du tidigare har accepterat villkoren. Exempel:

```powershell
Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Resultat:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 2/23/2018 7:43:00 PM
```

Använd den [Set AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) för att godkänna eller avvisa villkoren. Du behöver bara godkänna villkoren en gång per prenumeration för avbildningen. Exempel:

```powershell

$agreementTerms=Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzureRmMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept

```

Resultat:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : VNMTRJK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBJSS4GQ
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Distribuera med köp plan parametrar
Du kan distribuera en virtuell dator i prenumerationen efter accepterar du villkoren för avbildningen. I följande fragment visas när du använder den [Set AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) cmdleten Marketplace planera information för det Virtuella datorobjektet. Ett komplett skript för att skapa nätverksinställningarna för den virtuella datorn och slutför distributionen, finns det [exempel på PowerShell-skript](powershell-samples.md).

```powershell
...
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information
$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher "imagePlanPublisher" -Product "imagePlanProduct" -Name "imagePlanName"

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "imagePublisher" -Offer "imageOffer" -Skus "imageSku" -Version "imageVersion"
...
```
Du skickar sedan VM-konfiguration tillsammans med network configuration-objekt till den `New-AzureRmVM` cmdlet.

## <a name="next-steps"></a>Nästa steg
Så här skapar du en virtuell dator snabbt med `New-AzureRmVM` med hjälp av grundläggande avbildningen information, se [skapar en virtuell Windows-dator med PowerShell](quick-create-powershell.md).

Exempel på en PowerShell-skript för att se [skapa en virtuell dator som helt konfigurerade](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


