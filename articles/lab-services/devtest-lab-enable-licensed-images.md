---
title: Aktivera en licensierad avbildning i ditt labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig att aktivera en licensierad avbildning i Azure DevTest Labs med Azure portal
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
ms.openlocfilehash: 42f3378196b4e3e1dfe1234e801e87875f9c61fc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247661"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Aktivera en licensierad avbildning i ditt labb i Azure DevTest Labs

I Azure DevTest Labs är en licensierad avbildning en som innehåller villkor – vanligtvis från en tredje part – som måste godkännas innan avbildningen är tillgänglig för användare i laboratoriet. I följande avsnitt beskrivs hur du arbetar med licensierade bilder så att de kan användas för att skapa virtuella datorer.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Avgör om en licensierad avbildning är tillgänglig för användare
Det första steget mot att tillåta användare att skapa virtuella datorer från en licensierad avbildning är att se till att de allmänna villkoren har godkänts för licensierad avbildning. Följande steg visar hur du kan visa status för erbjudande för en licensierad avbildning och, om det behövs kan acceptera dess villkor.

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.

1. Listan över labbar, Välj önskade labbet.  

1. I den vänstra panelen under **inställningar**väljer **konfiguration och principer**.

1. I den vänstra panelen under **VM-BASER**väljer **Marketplace-avbildningar**. 

    ![Menyalternativet för Marketplace-avbildningar](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Visas en lista över alla tillgängliga marketplace-avbildningar, inklusive den **erbjuder STATUS** för varje bild.

    ![Lista över marketplace-avbildningar som visar status för erbjudande för varje bild](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    En licensierad avbildning visar ett erbjudande status 
    
    - **Villkor godkända:** licensierad avbildning är tillgänglig för användare att skapa virtuella datorer. 
    - **Villkor granskning behövs:** licensierad avbildning är inte tillgänglig för användare. Villkoren i licensen måste godkännas innan labbanvändare kan använda den för att skapa virtuella datorer. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Att göra en licensierad avbildning tillgängliga för labbanvändare
För att säkerställa en licensierad avbildning är tillgänglig för labbanvändare, måste labbägare med administratörsbehörighet först godkänna de allmänna villkoren för den licensierade bilden. Aktivera programdistribution för den prenumeration som är associerade med en licensierad avbildning automatiskt godkänner de juridiska villkoren och sekretesspolicyerna för avbildningen. [Arbeta med Marketplace-avbildningar på Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) tillhandahåller ytterligare information om programdistribution av marketplace-avbildningar.

Du kan aktivera programdistribution för en licensierad avbildning genom att följa dessa steg:

1. I den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040)går du till listan över **Marketplace-avbildningar**.

1. Identifiera en licensierad avbildning som du vill att användarna har åtkomst till men vars villkoren inte har godkänts. Du kan till exempel finns i en virtuell dator för datavetenskap som visar statusen **villkor godkända** eller **villkor granskning behövs**.

    ![Konfigurera programdistribution fönster](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Virtuella datorer för datavetenskap är avbildningar av Azure virtuella datorer, förinstallerade, konfigurerats och testats med flera populära verktyg som vanligen används för dataanalys, maskininlärning och AI-utbildning. [Introduktion till Azure Data Science Virtual Machine för Linux och Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) ger mycket information om Dsvm.
   >
   >

1. I den **erbjuder STATUS** kolumn för avbildningen, väljer **villkor granskning behövs**.

1. I fönstret Konfigurera programdistribution väljer **aktivera**.

    ![Konfigurera programdistribution fönster](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Du kan se flera prenumerationer som visas i fönstret Konfigurera programdistribution. Kontrollera att du aktiverar programdistribution endast för den avsedda prenumerationen.
   >
   >


1. Välj **Spara**. 

    I listan över marketplace-avbildningar, som bild nu visar **villkor godkända** och är tillgänglig för användare att skapa virtuella datorer.

> [!NOTE]
> Användare kan skapa en anpassad avbildning från en licensierad avbildning. Se [skapa en anpassad avbildning från en VHD-fil](devtest-lab-create-template.md) för mer information.
>
>


## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning från en virtuell dator](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Skapa en anpassad avbildning från en VHD-fil](devtest-lab-create-template.md)
- [Lägg till en virtuell dator i labbet](devtest-lab-add-vm.md)