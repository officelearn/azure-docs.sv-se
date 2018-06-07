---
title: Skapa en anpassad avbildning i Azure DevTest Labs från en virtuell dator | Microsoft Docs
description: Lär dig hur du skapar en anpassad avbildning i Azure DevTest Labs från en allokerad virtuell dator med hjälp av Azure portal
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 22f1579b2df2acdc736ed4c1d5cee64d096c320a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635924"
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

1. På den **anpassad bild** rutan Ange ett namn och beskrivning för den anpassade avbildningen. Den här informationen visas i listan över databaser när du skapar en virtuell dator. Den anpassade avbildningen innehåller OS-disken och alla datadiskar som är kopplade till den virtuella datorn.

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
