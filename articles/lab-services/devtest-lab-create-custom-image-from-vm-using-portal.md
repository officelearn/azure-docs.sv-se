---
title: Skapa en anpassad Azure DevTest Labs-avbildning från en virtuell dator | Microsoft Docs
description: Lär dig hur du skapar en anpassad avbildning i Azure DevTest Labs från en etablerad virtuell dator med Azure portal
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
ms.openlocfilehash: 780799103708483fb0092c7a4c15bfaa3269c073
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636490"
---
# <a name="create-a-custom-image-from-a-vm"></a>Skapa en anpassad avbildning från en virtuell dator

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Du kan skapa en anpassad avbildning från en etablerad virtuell dator och därefter använda den anpassa avbildningen för att skapa identiska virtuella datorer. Följande steg illustrerar hur du skapar en anpassad avbildning från en virtuell dator:

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.

1. Listan över labbar, Välj önskade labbet.  

1. På den testmiljön huvudfönstret väljer **Mina virtuella datorer**.
 
1. På den **Mina virtuella datorer** fönstret, Välj den virtuella datorn från vilken du vill skapa den anpassade avbildningen.

1. Fönstret för hantering av den virtuella datorn, Välj **Skapa anpassad avbildning (VHD)**.

    ![Skapa anpassad avbildning menyalternativ](./media/devtest-lab-create-template/create-custom-image.png)

1. På den **anpassad avbildning** rutan Ange ett namn och beskrivning för den anpassade avbildningen. Den här informationen visas i listan över baser när du skapar en virtuell dator. Den anpassade avbildningen innehåller OS-disken och alla datadiskar som är kopplade till den virtuella datorn.

    ![Skapa anpassad avbildningsfönstret](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Välj om sysprep kördes på den virtuella datorn. Om kommandot sysprep inte kördes på den virtuella datorn, kan du ange om du vill att sysprep som ska köras på den virtuella datorn när den anpassade avbildningen skapas.

1. Välj **OK** när du är klar för att skapa den anpassade avbildningen.

Efter några minuter, den anpassade avbildningen skapas och lagras i den testmiljön storage-konto. Bilden är tillgänglig i listan över grundläggande avbildningar när en lab användare vill skapa en ny virtuell dator.

![Anpassad avbildning som är tillgängliga i listan över grundläggande avbildningar](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Lägg till en virtuell dator i labbet](devtest-lab-add-vm.md)
