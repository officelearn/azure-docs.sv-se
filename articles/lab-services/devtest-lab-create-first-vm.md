---
title: Skapa din första virtuella datorn i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du skapar din första virtuella datorn i ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0240bbdf2055ac44f8836adfaad3bf6c44893b77
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Skapa din första virtuella datorn i ett labb i Azure DevTest Labs

När du först komma åt DevTest Labs och vill skapa din första virtuella datorn, kommer troligen gör du det med hjälp av en förinstallerade [grundläggande marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Vid ett senare tillfälle du kommer även att kunna välja en [anpassad avbildning och en formel](devtest-lab-add-vm.md) när du skapar flera virtuella datorer. 

Den här självstudiekursen vägleder dig genom att använda Azure portal för att lägga till den första virtuella datorn i ett labb i DevTest Labs.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Steg för att lägga till den första virtuella datorn i ett labb i Azure DevTest Labs
1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** från listan.
1. Välj labbet där du vill skapa den virtuella datorn från listan över övningar.  
1. På testmiljön **översikt** bladet väljer **+ Lägg till**.  

    ![VM-knappen Lägg till](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. På den **väljer du en bas** bladet Välj en marketplace-avbildning för den virtuella datorn.
1. På den **virtuella** bladet anger du ett namn för den nya virtuella datorn i den **virtuellt datornamn** textruta.

    ![Blad för labbet VM](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Ange en **användarnamn** som har beviljats administratörsbehörighet på den virtuella datorn.  
1. Ange ett lösenord i fältet med etiketten **skriver ett värde**.
1. Den **virtuella disktyp** avgör vilken lagringstyp som disk är tillåten för virtuella datorer i labbet.
1. Välj **storlek för virtuell dator** och välj en av de fördefinierade objekt som anger processorkärnor, ram-storlek och storleken på hårddisken på den virtuella datorn för att skapa.
1. Välj **artefakter** och - från listan över artefakter - Välj och konfigurera artefakter som du vill lägga till basavbildningen.
    **Obs:** om du inte har arbetat med DevTest Labs eller konfigurera artefakter, referera till den [lägga till en befintlig artefakt till en virtuell dator](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) avsnittet och sedan återvända hit när du är klar.
1. Välj **skapa** att lägga till den angivna virtuella datorn i labbet.

   Blad för labbet visar status för den virtuella datorn skapas - först som **skapa**, sedan som **kör** när den virtuella datorn har startats.

## <a name="next-steps"></a>Nästa steg
* När den virtuella datorn har skapats kan du ansluta till den virtuella datorn genom att välja **Anslut** på bladet för den virtuella datorn.
* Checka ut [lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md) fullständig information om hur du lägger till följande virtuella datorer i labbet.
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
