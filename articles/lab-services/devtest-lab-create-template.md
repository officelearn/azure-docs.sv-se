---
title: Skapa en anpassad Azure DevTest Labs-avbildning från en VHD-fil | Microsoft Docs
description: Lär dig hur du skapar en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure portal
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296169"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Skapa en anpassad avbildning från en VHD-fil

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg beskriver hur du skapar en anpassad avbildning från en VHD-fil med hjälp av Azure portal:

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.

1. Listan över labbar, Välj önskade labbet.  

1. På den testmiljön huvudfönstret väljer **konfiguration och principer**. 

1. På den **konfiguration och principer** väljer **anpassade avbildningar**.

1. På den **anpassade avbildningar** väljer **+ Lägg till**.

    ![Lägg till anpassad avbildning](./media/devtest-lab-create-template/add-custom-image.png)

1. Ange namnet på den anpassade avbildningen. Det här namnet visas i listan över grundläggande avbildningar när du skapar en virtuell dator.

1. Ange beskrivning av den anpassade avbildningen. Den här beskrivningen visas i listan över grundläggande avbildningar när du skapar en virtuell dator.

1. För **OS-typ**, väljer du antingen **Windows** eller **Linux**.

    - Om du väljer **Windows**, ange via kryssrutan om *sysprep* har körts på datorn. 
    - Om du väljer **Linux**, ange via kryssrutan om *avetablering* har körts på datorn. 

1. Välj en **VHD** från den nedrullningsbara menyn. Det här är den virtuella Hårddisken som ska användas för att skapa nya anpassade avbildningen. Om det behövs väljer du **överföra en virtuell Hårddisk med hjälp av PowerShell**.

1. Du kan också ange ett namn, plan erbjudandet och plan publisher om den avbildning som används för att skapa den anpassade avbildningen inte är en licensierad avbildning (som publicerats av Microsoft).

   - **Namn på prenumerationsavtal:** Ange namnet på Marketplace-avbildning (SKU) från den här anpassade avbildningen har skapats 
   - **Planera erbjudande:** Ange produktnyckel (erbjudandet) av Marketplace-avbildning från vilken den här anpassade avbildningen har skapats 
   - **Planera utgivare:** Ange utgivaren av Marketplace-avbildning från vilken den här anpassade avbildningen har skapats

   > [!NOTE]
   > Om den avbildning som du använder för att skapa en anpassad avbildning är **inte** en licensierad avbildning och sedan de här fälten är tomma och kan fyllas i om du väljer. Om avbildningen **är** en licensierad avbildning och sedan fälten är fylls i med information om supportavtalet automatiskt. Om du försöker ändra dem i det här fallet visas ett varningsmeddelande.
   >
   >

1. Välj **OK** att skapa den anpassade avbildningen.

Efter några minuter, den anpassade avbildningen skapas och lagras i den testmiljön storage-konto. Bilden är tillgänglig i listan över grundläggande avbildningar när en lab användare vill skapa en ny virtuell dator.

![Anpassad avbildning som är tillgängliga i listan över grundläggande avbildningar](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Lägg till en virtuell dator i labbet](./devtest-lab-add-vm.md)
