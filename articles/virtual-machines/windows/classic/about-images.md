---
title: "Om avbildningar för Windows-datorer | Microsoft Docs"
description: "Läs mer om hur bilder används med Windows-datorer i Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
ms.openlocfilehash: d421cee0becabdf81d865036d0c98b12b077152b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="about-images-for-windows-virtual-machines"></a>Om avbildningar för Windows-datorer
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om att hitta och använda bilder i Resource Manager-modellen finns [här](../../virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Arbeta med bilder

Du kan använda Azure PowerShell-modulen och Azure portal för att hantera bilderna som finns på Azure-prenumerationen. Azure PowerShell-modulen innehåller fler kommandoalternativ, så du kan hitta exakt vad du vill se eller göra. Azure portal tillhandahåller ett grafiskt användargränssnitt för många av de vanliga administrativa uppgifterna.

Här följer några exempel som använder Azure PowerShell-modulen.

* **Hämta alla bilder**:`Get-AzureVMImage`returnerar en lista över alla avbildningar som är tillgängliga i din aktuella prenumeration: bilderna och de som tillhandahålls av Azure eller partners. Listan kan vara stora. I nästa exempel visas hur du hämtar en kortare lista.
* **Hämta bild familjer**:`Get-AzureVMImage | select ImageFamily` hämtar en lista över avbildningen familjer genom att visa strängar **ImageFamily** egenskapen.
* **Hämta alla bilder i en specifik familj**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Hitta VM-avbildningar**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` denna cmdlet fungerar genom att filtrera egenskapen DataDiskConfiguration, som gäller endast för VM-avbildningar. Det här exemplet filtrerar också utdata till endast etikett- och namn.
* **Spara en generaliserad avbildning**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Spara en specialiserad avbildning**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > Parametern OSState krävs för att skapa en VM-avbildning som innehåller operativsystemets disk och anslutna datadiskar. Om du inte använder parametern skapar cmdleten en operativsystemavbildning. Värdet för parametern anger om bilden är generaliserad eller specialiserade, baserat på om operativsystemdisken har förberetts för återanvändning.

* **Ta bort en bild**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Nästa steg
Du kan också [skapa en Windows-dator med hjälp av Azure portal](tutorial.md).
