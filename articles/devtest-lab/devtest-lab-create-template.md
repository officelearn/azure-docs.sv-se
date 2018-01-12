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
ms.date: 01/10/2018
ms.author: v-craic
ms.openlocfilehash: d1f1b9948fb591484c107818a01e141932effbba
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Skapa en anpassad avbildning från en VHD-fil

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Stegvisa anvisningar

Följande steg beskriver hur du skapar en anpassad avbildning från en VHD-fil med hjälp av Azure portal:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**, och välj sedan **DevTest Labs** från listan.

1. Lista över labs, Välj önskade labbet.  

1. På den testmiljön huvudfönstret väljer **konfiguration och principer**. 

1. På den **konfiguration och principer** väljer **anpassade avbildningar**.

1. På den **anpassade avbildningar** väljer **+ Lägg till**.

    ![Lägg till anpassad bild](./media/devtest-lab-create-template/add-custom-image.png)

1. Ange namnet på den anpassade avbildningen. Det här namnet visas i listan över grundläggande bilder när du skapar en virtuell dator.

1. Ange en beskrivning av den anpassade avbildningen. Den här beskrivningen visas i listan över grundläggande bilder när du skapar en virtuell dator.

1. För **OS-typen**, väljer du antingen **Windows** eller **Linux**.

    - Om du väljer **Windows**, ange via kryssrutan om *sysprep* har körts på datorn. 
    - Om du väljer **Linux**, ange via kryssrutan om *avetablering* har körts på datorn. 

1. Välj en **VHD** från den nedrullningsbara menyn. Det här är den virtuella Hårddisken som ska användas för att skapa nya anpassade avbildningen. Om det behövs väljer att **överföra en virtuell Hårddisk med hjälp av PowerShell**.

1. Du kan också ange ett namn för energischema, plan erbjudandet och plan publisher om bilden används för att skapa den anpassade avbildningen inte har publicerats av Microsoft.

1. Välj **OK** att skapa den anpassade avbildningen.

Efter några minuter anpassad bild skapas och lagras i den labblagringskontot. När en lab-användare som vill skapa en ny virtuell dator, är bilden tillgänglig i listan över grundläggande bilder.

![Anpassad avbildning som är tillgängliga i listan över grundläggande bilder](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Lägga till en virtuell dator i labbet](./devtest-lab-add-vm.md)
