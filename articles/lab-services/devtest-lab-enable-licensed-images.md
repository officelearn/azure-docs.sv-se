---
title: Aktivera en licensierad avbildning i labbet i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du aktiverar en licensierad avbildning i Azure DevTest Labs med Azure-portalen
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 11b6553fe8aceef0d3d15977998dd870c275128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61294343"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Aktivera en licensierad avbildning i labbet i Azure DevTest Labs

I Azure DevTest Labs är en licensierad avbildning en som innehåller villkor – vanligtvis från en tredje part – som måste accepteras innan avbildningen är tillgänglig för användare i labbet. I följande avsnitt beskrivs hur du arbetar med licensierade avbildningar så att de kan användas för att skapa virtuella datorer.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Avgöra om en licensierad bild är tillgänglig för användare
Det första steget för att tillåta användare att skapa virtuella datorer från en licensierad avbildning är att se till att villkoren har accepterats för den licensierade bilden. Följande steg visar hur du kan visa erbjudandestatus för en licensierad bild och, om det behövs, acceptera dess villkor.

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.

1. Välj önskat labb i listan över labb.  

1. Välj **Konfiguration och principer**på den vänstra panelen under **INSTÄLLNINGAR.**

1. Välj **Marketplace-avbildningar**på den vänstra panelen under **VIRTUAL MACHINE BASES.** 

    ![Menyalternativ för Marketplace-bilder](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    En lista över alla tillgängliga marketplace-bilder visas, inklusive **ERBJUDANDESTATUS** FÖR varje bild.

    ![Lista över marketplace-bilder som visar erbjudandestatus för varje bild](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    En licensierad bild visar en erbjudandestatus på 
    
    - **Godkända villkor:** Den licensierade avbildningen är tillgänglig för användare för att skapa virtuella datorer. 
    - **Villkorsgranskning behövs:** den licensierade bilden är för närvarande inte tillgänglig för användare. Villkoren för licensen måste godkännas innan labbanvändare kan använda den för att skapa virtuella datorer. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Göra en licensierad avbildning tillgänglig för labbanvändare
För att se till att en licensierad avbildning är tillgänglig för labbanvändare måste en labbägare med administratörsbehörighet först acceptera villkoren för den licensierade avbildningen. Om du aktiverar programmatisk distribution för prenumerationen som är associerad med en licensierad avbildning accepteras automatiskt de juridiska villkoren och sekretesspolicyerna för den avbildningen. [Arbeta med Marketplace-avbildningar på Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) ger ytterligare information om programmatisk distribution av marketplace-avbildningar.

Du kan aktivera programmatisk distribution för en licensierad avbildning genom att följa dessa steg:

1. Gå till listan över **Marketplace-avbildningar**i [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Identifiera en licensierad avbildning som du vill att användarna ska ha åtkomst till men vars villkor inte har accepterats. Du kan till exempel se en virtuell dator för datavetenskap som visar statusen för antingen **godkända villkor** eller **villkorsgranskning som behövs**.

    ![Konfigurera fönstret Programmatisk distribution](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Virtuella datorer med datavetenskap är Azure Virtual Machine-avbildningar, förinstallerade, konfigurerade och testade med flera populära verktyg som ofta används för dataanalys, maskininlärning och AI-utbildning. [Introduktion till Virtual Machine för Azure Data Science för Linux och Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) innehåller en hel del information om DSVMs.
   >
   >

1. I kolumnen **ERBJUDANDESTATUS** för bilden väljer du **Villkorsgranskning behövs**.

1. Välj **Aktivera**i fönstret Konfigurera programmatisk distribution .

    ![Konfigurera fönstret Programmatisk distribution](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Du kan se flera prenumerationer i fönstret Konfigurera programmatisk distribution. Kontrollera att du aktiverar programmatisk distribution endast för den avsedda prenumerationen.
   >
   >


1. Välj **Spara**. 

    I listan över marketplace-avbildningar visar den avbildningen nu **villkor som accepteras** och är tillgänglig för användare att skapa virtuella datorer.

> [!NOTE]
> Användare kan skapa en anpassad avbildning från en licensierad avbildning. Mer information finns i [Skapa en anpassad avbildning från en VHD-fil.](devtest-lab-create-template.md)
>
>


## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade bilder eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning från en virtuell dator](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Skapa en anpassad avbildning från en VHD-fil](devtest-lab-create-template.md)
- [Lägga till en virtuell dator i labbet](devtest-lab-add-vm.md)