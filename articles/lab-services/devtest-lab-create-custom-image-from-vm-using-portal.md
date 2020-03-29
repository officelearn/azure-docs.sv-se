---
title: Skapa en anpassad avbildning i Azure DevTest Labs från en virtuell dator | Microsoft-dokument
description: Lär dig hur du skapar en anpassad avbildning i Azure DevTest Labs från en etablerad virtuell dator med Azure-portalen
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
ms.openlocfilehash: 07f3b60b9218f74bb3a778daa27f31687c4538b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60868507"
---
# <a name="create-a-custom-image-from-a-vm"></a>Skapa en anpassad avbildning från en virtuell dator

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Du kan skapa en anpassad avbildning från en etablerad virtuell dator och sedan använda den anpassade avbildningen för att skapa identiska virtuella datorer. Följande steg illustrerar hur du skapar en anpassad avbildning från en virtuell dator:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.

1. Välj önskat labb i listan över labb.  

1. I labbets huvudfönster väljer du **Mina virtuella datorer**.
 
1. I fönstret **Mina virtuella datorer** väljer du den virtuella dator som du vill skapa den anpassade avbildningen från.

1. I hanteringsfönstret för den virtuella datorn väljer du **Skapa anpassad avbildning (VHD).**

    ![Skapa anpassat bildmenyalternativ](./media/devtest-lab-create-template/create-custom-image.png)

1. Ange ett namn och en beskrivning för den anpassade bilden i **fönstret Anpassad** bild. Den här informationen visas i listan över baser när du skapar en virtuell dator. Den anpassade avbildningen kommer att innehålla OS-disken och alla datadiskar som är anslutna till den virtuella datorn.

    ![Skapa anpassat bildfönster](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Välj om sysprep kördes på den virtuella datorn. Om sysprep inte kördes på den virtuella datorn anger du om du vill att sysprep ska köras på den virtuella datorn när den anpassade avbildningen skapas.

1. Välj **OK** när du är klar om du vill skapa den anpassade bilden.

Efter några minuter skapas den anpassade avbildningen och lagras i labbets lagringskonto. När en labbanvändare vill skapa en ny virtuell dator är avbildningen tillgänglig i listan över basavbildningar.

![Anpassad bild tillgänglig i listan över basbilder](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade bilder eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Lägga till en virtuell dator i labbet](devtest-lab-add-vm.md)
