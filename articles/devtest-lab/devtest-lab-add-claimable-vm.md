---
title: "Lägg till en claimable virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs"
description: "Lär dig hur du lägger till en claimable virtuell dator i ett labb i Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: tarcher
ms.openlocfilehash: 98950d72e90b0e178bae2fffa7644fd824a25eea
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Lägg till en claimable virtuell dator i ett labb i Azure DevTest Labs
Du lägger till en claimable virtuell dator i ett labb på liknande sätt att hur du [lägga till en standard VM](devtest-lab-add-vm.md) – från en *grundläggande* som är antingen en [anpassad avbildning](devtest-lab-create-template.md), [formeln](devtest-lab-manage-formulas.md) , eller [Marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Den här självstudiekursen vägleder dig genom att använda Azure portal för att lägga till en claimable virtuell dator i ett labb i DevTest Labs och visas hur en användare följer för att kräva att den virtuella datorn.

> [!NOTE]
> Om du distribuerar lab virtuella datorer via [Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md), du kan skapa claimable virtuella datorer genom att ange den **allowClaim** egenskap till true i egenskapsavsnittet.
>
>

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Steg för att lägga till en claimable virtuell dator i ett labb i Azure DevTest Labs
1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
1. Välj från listan över labs labbet där du vill skapa claimable VM.  
1. På testmiljön **översikt** bladet väljer **+ Lägg till**.  

    ![VM-knappen Lägg till](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. På den **väljer du en bas** bladet Välj en bas för den virtuella datorn.
1. På den **virtuella** bladet anger du ett namn för den nya virtuella datorn i den **virtuellt datornamn** textruta.

    ![Blad för labbet VM](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Ange en **användarnamn** som har beviljats administratörsbehörighet på den virtuella datorn.  
1. Om du vill använda ett lösenord som lagras i din [hemliga store](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)väljer **använda en sparad hemlighet**, och ange ett nyckelvärde som motsvarar ditt hemliga (lösenord). Annars kan ange ett lösenord i fältet med etiketten **skriver ett värde**.
1. Den **virtuella disktyp** avgör vilken lagringstyp som disk är tillåten för virtuella datorer i labbet.
1. Välj **storlek för virtuell dator** och välj en av de fördefinierade objekt som anger processorkärnor, ram-storlek och storleken på hårddisken på den virtuella datorn för att skapa.
1. Välj **artefakter** och listan över artefakter och välj och konfigurera artefakter som du vill lägga till basavbildningen. Om du inte har arbetat med DevTest Labs eller konfigurera artefakter, referera till den [lägga till en befintlig artefakt till en virtuell dator](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) avsnittet och sedan återvända hit när du är klar.
1. Välj **avancerade inställningar** att konfigurera Virtuellt Nätverksalternativ och upphör att gälla. Under **anspråk alternativ**, Välj **Ja** så att datorn claimable.

  ![Välja att göra den virtuella datorn claimable.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Om du vill visa eller kopiera Azure Resource Manager-mallen finns i [spara Azure Resource Manager-mall](devtest-lab-add-vm.md#save-azure-resource-manager-template) avsnittet och återvända hit när du är klar.
1. Välj **skapa** att lägga till den angivna virtuella datorn i labbet.
1. Blad för labbet visar status för den virtuella datorn skapas - först som **skapa**, sedan som **kör** när den virtuella datorn har startats.


## <a name="using-a-claimable-vm"></a>Med hjälp av en claimable VM

En användare kan göra anspråk på någon virtuell dator i listan över ”Claimable virtuella datorer” genom att göra något av följande:

* I listan över ”Claimable virtuella datorer” längst ned på bladet för den testmiljön översikt, högerklicka på någon av de virtuella datorerna i listan och väljer **anspråk datorn**.

 ![Begäran om en specifik claimable VM.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Längst upp i den **översikt** bladet välj **anspråk någon**. En slumpmässig virtuell dator är tilldelad från listan över claimable virtuella datorer.

 ![Begära alla claimable VM.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


När en användare anspråk som en virtuell dator, flyttas till sin lista över ”mina virtuella datorer” och är inte längre claimable av någon annan användare.

## <a name="next-steps"></a>Nästa steg
* När den virtuella datorn har skapats kan du ansluta till den virtuella datorn genom att välja **Anslut** på bladet för den virtuella datorn.
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)
