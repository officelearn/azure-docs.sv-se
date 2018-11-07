---
title: Skapa din första virtuella dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du skapar din första virtuella dator i ett labb i Azure DevTest Labs
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
ms.openlocfilehash: 93ce9feaf52282b9477d49eaf270d6d89dca7811
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232536"
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Skapa din första virtuella dator i ett labb i Azure DevTest Labs

När du först få åtkomst till labb och vill skapa din första virtuella dator, du kommer förmodligen göra det med hjälp av en förinstallerade [grundläggande marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Vid ett senare tillfälle du kommer även att kunna välja från en [anpassad avbildning och en formel](devtest-lab-add-vm.md) när du skapar fler virtuella datorer. 

Den här självstudiekursen beskriver hur du använder Azure-portalen att lägga till din första virtuella dator till ett labb i DevTest Labs.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Hur du lägger till din första virtuella dator till ett labb i Azure DevTest Labs
1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. I listan över labbar Välj labb där du vill skapa den virtuella datorn.  
1. På testmiljön **översikt** bladet väljer **+ Lägg till**.  

    ![Lägg till VM-knapp](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. På den **väljer bas** bladet och välja en marketplace-avbildning för den virtuella datorn.
1. På den **VM** bladet anger du ett namn för den nya virtuella datorn i den **virtuellt datornamn** textrutan.

    ![Blad för labbet VM](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Ange en **användarnamn** som har beviljats administratörsbehörighet på den virtuella datorn.  
1. Ange ett lösenord i textfältet märkta **skriver ett värde**.
1. Den **disktyp för virtuell dator** avgör vilken typ av premiumlagring tillåts för virtuella datorer i labbet.
1. Välj **VM-storlek** och välj en av de fördefinierade objekt som anger processorkärnor, RAM-storleken och storleken på hårddisken på den virtuella datorn för att skapa.
1. Välj **artefakter** och - från listan över artefakter - Välj och konfigurera de artefakter som du vill lägga till i basavbildningen.
    **Obs:** om du är nybörjare på DevTest Labs eller konfigurera artefakter, referera till den [lägga till en befintlig artefakt i en virtuell dator](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) avsnittet och återgå sedan här när du är klar.
1. Välj **skapa** att lägga till den angivna virtuella datorn till labbet.

   Blad för labbet visar status för den Virtuella datorns Skapa - först som **skapa**, sedan som **kör** när den virtuella datorn har startats.

## <a name="next-steps"></a>Nästa steg
* När den virtuella datorn har skapats kan du ansluta till den virtuella datorn genom att välja **Connect** på bladet för den virtuella datorn.
* Kolla in [lägga till en virtuell dator i en labb](devtest-lab-add-vm.md) fullständig information om att lägga till efterföljande virtuella datorer i labbet.
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
