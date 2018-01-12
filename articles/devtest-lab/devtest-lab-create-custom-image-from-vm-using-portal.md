---
title: "Skapa en anpassad avbildning i Azure DevTest Labs från en virtuell dator | Microsoft Docs"
description: "Lär dig hur du skapar en anpassad avbildning i Azure DevTest Labs från en allokerad virtuell dator med hjälp av Azure portal"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: v-craic
ms.openlocfilehash: dc315bcc625ea98244bb5804ce6ff1c13d0ec7b1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-custom-image-from-a-vm"></a>Skapa en anpassad avbildning från en virtuell dator

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Stegvisa anvisningar

Du kan skapa en anpassad avbildning från en allokerad virtuell dator och därefter använda den anpassade bilden för att skapa identiska virtuella datorer. Följande steg visar hur du skapar en anpassad avbildning från en virtuell dator:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**, och välj sedan **DevTest Labs** från listan.

1. Lista över labs, Välj önskade labbet.  

1. På den testmiljön huvudfönstret väljer **Mina virtuella datorer**.
 
1. På den **Mina virtuella datorer** rutan, Välj den virtuella datorn från vilken du vill skapa den anpassade avbildningen.

1. På den virtuella datorn hanteringsfönstret, markera **Skapa anpassad avbildning (VHD)**.

    ![Skapa anpassad avbildning menyobjekt](./media/devtest-lab-create-template/create-custom-image.png)

1. På den **anpassad bild** rutan Ange ett namn och beskrivning för den anpassade avbildningen. Den här informationen visas i listan över databaser när du skapar en virtuell dator.

    ![Skapa anpassad avbildning fönsterruta](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Välj om sysprep körs på den virtuella datorn. Om inte kört sysprep på den virtuella datorn, kan du ange om du vill att sysprep körs när en virtuell dator skapas från den här anpassade avbildningen.

1. Välj **OK** när du är klar för att skapa den anpassade avbildningen.

Efter några minuter anpassad bild skapas och lagras i den labblagringskontot. När en lab-användare som vill skapa en ny virtuell dator, är bilden tillgänglig i listan över grundläggande bilder.

![Anpassad avbildning som är tillgängliga i listan över grundläggande bilder](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Lägga till en virtuell dator i labbet](devtest-lab-add-vm.md)
