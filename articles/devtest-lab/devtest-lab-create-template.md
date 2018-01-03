---
title: "Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil | Microsoft Docs"
description: "Lär dig hur du skapar en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure portal"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: 7605a65d784a9586a4d88625996f4a1c8f154e9d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Skapa en anpassad avbildning från en VHD-fil

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Stegvisa anvisningar

Följande steg beskriver hur du skapar en anpassad avbildning från en VHD-fil med hjälp av Azure portal:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Fler tjänster** och välj sedan **DevTest Labs** från listan.

1. Lista över labs, Välj önskade labbet.  

1. På den testmiljön bladet välj **Configuration**. 

1. Om labbet **Configuration** bladet väljer **anpassade avbildningar (VHD)**.

1. På den **anpassade avbildningar** bladet väljer **+ Lägg till**.

    ![Lägg till anpassad bild](./media/devtest-lab-create-template/add-custom-image.png)

1. Ange namnet på den anpassade avbildningen. Det här namnet visas i listan över grundläggande bilder när du skapar en virtuell dator.

1. Ange en beskrivning av den anpassade avbildningen. Den här beskrivningen visas i listan över grundläggande bilder när du skapar en virtuell dator.

1. Välj **VHD**.

1. Från den **VHD** bladet Välj önskad VHD-filen.

1. Välj **OK** att stänga den **VHD** bladet.

1. Välj **Operativsystemskonfiguration**.

1. På den **Operativsystemskonfiguration** väljer du antingen **Windows** eller **Linux**.

1. Om **Windows** har valts anger via kryssrutan om *Sysprep* har körts på datorn. 

1. Välj **OK** att stänga den **Operativsystemets konfiguration** bladet.

1. Välj **OK** att skapa den anpassade avbildningen.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>Nästa steg

- [Lägga till en virtuell dator i labbet](./devtest-lab-add-vm.md)
