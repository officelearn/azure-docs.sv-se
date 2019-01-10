---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f7539ed5083a386ef05134bea36426f4a360afad
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192245"
---
## <a name="shared-image-management"></a>Hantering av delade avbildningar 

Här följer några exempel på vanliga hanteringsuppgifter och hur du utför dem med hjälp av PowerShell.

Lista alla gallerier efter namn.

```azurepowershell-interactive
$galleries = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Lista alla definitioner för avbildning efter namn.

```azurepowershell-interactive
$imageDefinitions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

Lista alla avbildningsversioner efter namn.

```azurepowershell-interactive
$imageVersions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Ta bort en Bildversion. Det här exemplet tar bort versionsnumret för avbildningen med namnet *1.0.0*.

```azurepowershell-interactive
Remove-AzureRmGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





