---
title: Skapa en Azure DevTest Labs anpassad avbildning från en VHD-fil | Microsoft Docs
description: Lär dig hur du skapar en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure Portal
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 853c138c8cf73b41b0cebb6c1d349865e18eab6a
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896993"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Skapa en anpassad avbildning från en VHD-fil

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg beskriver hur du skapar en anpassad avbildning från en VHD-fil med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**och välj sedan **DevTest Labs** i listan.

1. I listan med labb väljer du önskat labb.  

1. Välj **konfiguration och principer**i Labbets huvud fönster. 

1. I fönstret **konfiguration och principer** väljer du **anpassade avbildningar**.

1. I fönstret **anpassade avbildningar** väljer du **+ Lägg till**.

    ![Lägg till anpassad bild](./media/devtest-lab-create-template/add-custom-image.png)

1. Ange namnet på den anpassade avbildningen. Det här namnet visas i listan med bas avbildningar när du skapar en virtuell dator.

1. Ange beskrivningen av den anpassade avbildningen. Den här beskrivningen visas i listan med bas avbildningar när du skapar en virtuell dator.

1. För **OS-typ**väljer du antingen **Windows** eller **Linux**.

    - Om du väljer **Windows**anger du via kryss rutan om *Sysprep* har körts på datorn. 
    - Om du väljer **Linux**anger du via kryss rutan om *avetablering* har körts på datorn. 

1. Välj en **virtuell hård disk** på den nedrullningsbara menyn. Detta är den virtuella hård disk som ska användas för att skapa den nya anpassade avbildningen. Om det behövs väljer du att **Ladda upp en virtuell hård disk med PowerShell**.

1. Du kan också ange ett plan namn, planera erbjudandet och planera utgivare om avbildningen som används för att skapa den anpassade avbildningen inte är en licensierad avbildning (publiceras av Microsoft).

   - **Plan namn:** Ange namnet på Marketplace-avbildningen (SKU) från vilken den här anpassade avbildningen skapas 
   - **Plan erbjudande:** Ange produkten (erbjudandet) för Marketplace-avbildningen som den här anpassade avbildningen skapas från 
   - **Planera utgivare:** Ange utgivaren av Marketplace-avbildningen som den här anpassade avbildningen skapas från

   > [!NOTE]
   > Om avbildningen som du använder för att skapa en anpassad avbildning **inte** är en licensierad avbildning är dessa fält tomma och kan fyllas i om du väljer. Om avbildningen **är** en licensierad avbildning fylls fälten i automatiskt med plan informationen. Om du försöker ändra dem i det här fallet visas ett varnings meddelande.
   >
   >

1. Välj **OK** för att skapa den anpassade avbildningen.

Efter några minuter skapas den anpassade avbildningen och lagras i Labbets lagrings konto. När en labb användare vill skapa en ny virtuell dator är avbildningen tillgänglig i listan med bas avbildningar.

![Anpassad avbildning tillgänglig i listan med bas avbildningar](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogg inlägg

- [Anpassade bilder eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade bilder mellan Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Lägg till en virtuell dator i labbet](./devtest-lab-add-vm.md)
