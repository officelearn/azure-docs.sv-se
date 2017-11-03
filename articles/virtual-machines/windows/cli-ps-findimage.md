---
title: "Välj Windows VM-avbildningar i Azure | Microsoft Docs"
description: "Lär dig hur du använder Azure PowerSHell för att fastställa utgivare, erbjudande, SKU och version för Marketplace VM-avbildningar."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.openlocfilehash: c9b35ff5f3fbd33639805b5a4f105df32562a691
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Hitta Windows VM-avbildningar i Azure Marketplace med Azure PowerShell

Det här avsnittet beskriver hur du använder Azure PowerShell för att hitta VM-avbildningar i Azure Marketplace. Använd informationen för att ange en Marketplace-avbildning när du skapar en virtuell Windows-dator.

Kontrollera att du har installerat och konfigurerat senast [Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps).



## <a name="table-of-commonly-used-windows-images"></a>Tabell med vanliga Windows-avbildningar
| PublisherName | Erbjudande | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016 Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 Datacenter med behållare |
| MicrosoftWindowsServer |WindowsServer |2016 Nano Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008 R2 SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016 WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2 WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>Söka efter specifika avbildningar


När du skapar en ny virtuell dator med Azure Resource Manager måste du i vissa fall ange en avbildning med följande kombination av egenskaper:

* Utgivare
* Erbjudande
* SKU

Till exempel använda dessa värden med den [Set AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) PowerShell-cmdlet eller med en mall för gruppen av resurs som du måste ange vilken typ av virtuell dator som ska skapas.

Om du behöver kunna bedöma dessa värden kan du köra den [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), och [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) -cmdletar för att navigera bilder. Du kan bestämma dessa värden.

1. Visa en lista över avbildningsutgivare.
2. Visa en lista över erbjudanden från en viss utgivare.
3. Visa en lista över SKU:er för ett visst erbjudande.

Börja med att skapa en lista över utgivare med hjälp av följande kommandon:

```azurepowershell-interactive
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Fyll i namnet på den valda utgivaren och kör följande kommandon:

```azurepowershell-interactive
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Fyll i namnet på det valda erbjudandet och kör följande kommandon:

```azurepowershell-interactive
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Från utdata från den `Get-AzureRMVMImageSku` kommando du har all information du behöver ange bild för en ny virtuell dator.

Följande visar ett fullständigt exempel:

```azurepowershell-interactive
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

För utgivaren ”MicrosoftWindowsServer”:

```azurepowershell-interactive
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Resultat:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

För erbjudandet ”WindowsServer”:

```azurepowershell-interactive
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
2016-Nano-Server
```

Kopiera det valda SKU-namnet från listan så har du all information om `Set-AzureRMVMSourceImage` PowerShell-cmdleten eller om en resursgruppmall.

## <a name="next-steps"></a>Nästa steg
Du kan nu välja exakt den avbildning som du vill använda. För att snabbt skapa en virtuell dator med hjälp av avbildningsinformationen som du har hittat finns [skapar en virtuell Windows-dator med PowerShell](quick-create-powershell.md).
