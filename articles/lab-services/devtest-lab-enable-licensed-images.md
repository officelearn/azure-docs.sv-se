---
title: Aktivera en licensierad avbildning i ditt labb i Azure DevTest Labs | Microsoft Docs
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
ms.openlocfilehash: 0e5de93f8a10d27c28b3f07567f9b6fa7e41d482
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787558"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Aktivera en licensierad avbildning i ditt labb i Azure DevTest Labs

I Azure DevTest Labs är en licensierad bilden en som innehåller villkor – vanligtvis från en tredje part – som måste godkännas innan avbildningen är tillgänglig för användare i labbet. I följande avsnitt beskrivs hur du arbetar med licensierade bilder så att de kan användas för att skapa virtuella datorer.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Avgöra om en licensierad avbildning är tillgängliga för användare
Det första steget för att tillåta användare att skapa virtuella datorer från en licensierad avbildning är att se till att de allmänna villkoren har godkänts för licensierade avbildningen. Följande steg visar hur du kan visa status för erbjudande för en licensierad avbildning och, om det behövs, acceptera dess villkor.

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**, och välj sedan **DevTest Labs** från listan.

1. Lista över labs, Välj önskade labbet.  

1. I den vänstra rutan under **inställningar**väljer **konfiguration och principer**.

1. I den vänstra rutan under **VIRTUELLA DATORBASER**väljer **Marketplace-bilder**. 

    ![Menyalternativet för Marketplace-bilder](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Visas en lista över alla tillgängliga marketplace-avbildningar, inklusive den **erbjuder STATUS** för varje bild.

    ![Lista över marketplace-bilder som visar status för erbjudande för varje avbildning](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    En licensierad bild visar ett erbjudande status 
    
    - **Villkoren som accepteras:** licensierade bilden är tillgänglig för användarna att skapa virtuella datorer. 
    - **Termer granska behövs:** licensierade bilden är inte tillgänglig för användare. De allmänna villkoren för måste godkännas innan lab-användare kan använda den för att skapa virtuella datorer. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Göra en avbildning av licensierade tillgängliga för lab-användare
Om du vill kontrollera en licensierad avbildning är tillgänglig för lab-användare, måste en labbägaren med administratörsbehörigheter först acceptera villkoren för licensierade avbildningen. Aktivera programdistribution för den prenumeration som är kopplade till en licensierad avbildning automatiskt godkänner juridiska villkor och sekretesspolicy för avbildningen. [Arbeta med Marketplace-bilder på Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) tillhandahåller ytterligare information om programdistribution i marketplace-bilder.

Du kan aktivera programdistribution för en licensierad avbildning genom att följa dessa steg:

1. I den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), gå till listan över **Marketplace-bilder**.

1. Identifiera en licensierad avbildning som du vill att användarna ska ha åtkomst, men vars villkoren har inte accepterats. Du kan till exempel se en datavetenskap virtuell dator som visar statusen **accepterat villkoren** eller **termer granska behövs**.

    ![Konfigurera programdistribution fönster](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Data är vetenskap virtuell dator i Azure-avbildningar, förinstallerat, konfigurerats och testats med flera populära verktyg som används ofta för dataanalys, machine learning och AI utbildning. [Introduktion till Azure datavetenskap virtuell dator för Linux och Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) ger mycket information om DSVMs.
   >
   >

1. I den **erbjuder STATUS** för avbildningen, Välj **termer granska behövs**.

1. I fönstret Konfigurera programdistribution Välj **aktivera**.

    ![Konfigurera programdistribution fönster](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Du kan se flera prenumerationer i fönstret Konfigurera programdistribution. Kontrollera att du aktiverar programdistribution endast för den avsedda prenumerationen.
   >
   >


1. Välj **Spara**. 

    I listan över marketplace-bilder som bild nu visar **accepterat villkoren** och är tillgänglig för användare att skapa virtuella datorer.

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
- [Lägga till en virtuell dator i labbet](devtest-lab-add-vm.md)