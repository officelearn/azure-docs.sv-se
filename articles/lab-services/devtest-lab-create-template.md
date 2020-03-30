---
title: Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil | Microsoft-dokument
description: Lär dig hur du skapar en anpassad avbildning i Azure DevTest Labs från en VHD-fil med Azure-portalen
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61296169"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Skapa en anpassad avbildning från en VHD-fil

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

I följande steg kan du skapa en anpassad avbildning från en VHD-fil med Azure-portalen:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.

1. Välj önskat labb i listan över labb.  

1. I labbets huvudfönster väljer du **Konfiguration och principer**. 

1. Välj **Anpassade avbildningar**i fönstret **Konfiguration och principer** .

1. Välj **+Lägg till**i fönstret **Anpassade bilder** .

    ![Lägg till anpassad bild](./media/devtest-lab-create-template/add-custom-image.png)

1. Ange namnet på den anpassade bilden. Det här namnet visas i listan över basavbildningar när du skapar en virtuell dator.

1. Ange beskrivningen av den anpassade bilden. Den här beskrivningen visas i listan över basavbildningar när du skapar en virtuell dator.

1. För **OS-typ**väljer du antingen **Windows** eller **Linux**.

    - Om du väljer **Windows**anger du via kryssrutan om *sysprep* har körts på datorn. 
    - Om du väljer **Linux**anger du via kryssrutan om *avetablera* har körts på datorn. 

1. Välj en **virtuell hårddisk** på den nedrullningsbara menyn. Det här är den virtuella hårddisken som ska användas för att skapa den nya anpassade avbildningen. Om det behövs väljer du att **ladda upp en virtuell hårddisk med PowerShell**.

1. Du kan också ange ett plannamn, ett abonnemangserbjudande och en planutgivare om den bild som används för att skapa den anpassade avbildningen inte är en licensierad avbildning (publicerad av Microsoft).

   - **Planens namn:** Ange namnet på den Marketplace-avbildning (SKU) som den här anpassade avbildningen skapas från 
   - **Planera erbjudande:** Ange produkten (erbjudandet) för Marketplace-avbildningen som den här anpassade avbildningen skapas från 
   - **Planera utgivare:** Ange utgivaren av Marketplace-avbildningen som den här anpassade avbildningen skapas från

   > [!NOTE]
   > Om bilden du använder för att skapa en anpassad avbildning **inte** är en licensierad avbildning är dessa fält tomma och kan fyllas i om du vill. Om bilden **är** en licensierad bild fylls fälten i automatiskt med planinformationen. Om du försöker ändra dem i det här fallet visas ett varningsmeddelande.
   >
   >

1. Välj **OK** om du vill skapa den anpassade bilden.

Efter några minuter skapas den anpassade avbildningen och lagras i labbets lagringskonto. När en labbanvändare vill skapa en ny virtuell dator är avbildningen tillgänglig i listan över basavbildningar.

![Anpassad bild tillgänglig i listan över basbilder](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade bilder eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Lägga till en virtuell dator i labbet](./devtest-lab-add-vm.md)
