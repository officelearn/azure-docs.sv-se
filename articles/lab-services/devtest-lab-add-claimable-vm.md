---
title: Skapa och hantera tillgängliga virtuella datorer i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en virtuell virtuell dator till ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2018
ms.author: spelluru
ms.openlocfilehash: 3bfb674fa66f0701a099d237f4e760453c7b6a6e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232135"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Skapa och hantera tillgängliga virtuella datorer i Azure DevTest Labs
Du lägger till en virtuell dator till ett labb i liknande sätt som hur du [lägga till en standard virtuell dator](devtest-lab-add-vm.md) – från en *grundläggande* som är antingen en [anpassad avbildning](devtest-lab-create-template.md), [formeln](devtest-lab-manage-formulas.md) , eller [Marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Den här självstudiekursen beskriver hur du använder Azure-portalen för att lägga till en virtuell dator i ett labb i DevTest Labs och visar processerna som en användare följer för att begära och avsäga sig den virtuella datorn.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Hur du lägger till en virtuell dator till ett labb i Azure DevTest Labs
1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. I listan över labbar Välj labb där du vill skapa virtuell dator.  
1. På testmiljön **översikt** väljer **+ Lägg till**.  

    ![Lägg till VM-knapp](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. På den **väljer bas** väljer du den base för den virtuella datorn.
1. I den **VM** rutan Ange ett namn för den nya virtuella datorn i den **virtuellt datornamn** textrutan.

    ![Lab VM-fönstret](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Ange en **användarnamn** som har beviljats administratörsbehörighet på den virtuella datorn.  
1. Om du vill använda ett lösenord som lagras i din [Arkiv för hemligheter](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)väljer **använder en sparad hemlighet**, och ange ett nyckelvärde som motsvarar din hemlighet (lösenord). Annars kan ange ett lösenord i textfältet märkta **skriver ett värde**.
1. Den **disktyp för virtuell dator** avgör vilken typ av premiumlagring tillåts för virtuella datorer i labbet.
1. Välj **VM-storlek** och välj en av de fördefinierade objekt som anger processorkärnor, RAM-storleken och storleken på hårddisken på den virtuella datorn för att skapa.
1. Välj **artefakter** och listan över artefakter, Välj och konfigurera artefakter som du vill lägga till i basavbildningen. Om du är nybörjare på DevTest Labs eller konfigurera artefakter, referera till den [lägga till en befintlig artefakt i en virtuell dator](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) avsnittet och återgå sedan här när du är klar.
1. Välj **avancerade inställningar** att konfigurera Virtuellt datorns Nätverksalternativ och alternativ för upphör att gälla. Under **anspråk alternativ**, Välj **Ja** att göra en dator som är tillgängliga.

  ![Välja att göra den virtuella datorn som är tillgängliga.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Om du vill visa eller kopiera Azure Resource Manager-mallen finns i [spara Azure Resource Manager-mall](devtest-lab-add-vm.md#save-azure-resource-manager-template) avsnitt och komma tillbaka hit när du är klar.
1. Välj **skapa** att lägga till den angivna virtuella datorn till labbet.

   Status för den virtuella datorn skapas visas först som **skapa**, sedan som **kör** när den virtuella datorn har startats.

> [!NOTE]
> Om du distribuerar labbet virtuella datorer via [Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md), du kan skapa tillgängliga virtuella datorer genom att ange den **allowClaim** egenskapen på Sant i egenskapsavsnittet.
>
>

## <a name="using-a-claimable-vm"></a>Med hjälp av en virtuell dator

En användare kan göra anspråk på en virtuell dator i listan över ”tillgängliga virtuella datorer” genom att göra något av följande:

* I listan ”tillgängliga virtuella datorer” längst ned på den lab ”översikt” fönstret, högerklicka på någon av de virtuella datorerna i listan och välj **anspråk datorn**.

 ![Begär en specifik virtuell dator.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Längst ned i fönstret ”Översikt” Välj **gör anspråk på någon**. En slumpmässig virtuell dator är tilldelad i listan med tillgängliga virtuella datorer.

 ![Begära valfri virtuell dator.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


När en användare anspråk som en virtuell dator, flyttas till sin lista över ”mina virtuella datorer” och är inte längre tillgängliga av någon annan användare.

## <a name="unclaim-a-vm"></a>Avsäga sig en virtuell dator

När en användare har slutförts med hjälp av en anspråk virtuell dator och vill göra den tillgänglig för någon annan, returnerar de anspråk VM i listan över tillgängliga virtuella datorer genom att göra något av följande:

- Listan över ”mina virtuella datorer”, högerklicka på någon av de virtuella datorerna i listan över – eller välj dess ellips (...) och välj **Unclaim**.

  ![Avsäga sig en virtuell dator i listan över virtuella datorer.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- I listan över ”mina virtuella datorer”, väljer du en virtuell dator för att öppna hanteringsfönstret av dess och välj sedan **Unclaim** från den översta menyraden.

  ![Avsäga sig en virtuell dator i fönstret för hantering av den virtuella datorn.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

När en användare unclaims en virtuell dator, har de inte längre några behörigheter för den specifika lab VM.

### <a name="transferring-the-data-disk"></a>Överföring av data-disk
Om en virtuell dator som har en datadisk som är kopplade till den och en användare unclaims den, datadisken förblir med den virtuella datorn. En annan användare anspråk när sedan att VM, den nya användaren anspråk datadisken samt den virtuella datorn.

Detta kallas ”överföring av data-disk”. Datadisken blir tillgängliga i den nya användarens lista över **Moje datové disky** för dem att hantera.

![Avsäga sig datadiskar.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Nästa steg
* När den har skapats kan du ansluta till den virtuella datorn genom att välja **Connect** dess fönstret för hantering.
* Utforska den [DevTest Labs Azure Resource Manager-mall snabbstartsgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
