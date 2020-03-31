---
title: Skapa och hantera anspråksbara virtuella datorer i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du lägger till en anspråksbar virtuell dator i ett labb i Azure DevTest Labs
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
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 13d642597fdf5d0eae6c6fd4f0cab16181f033c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270802"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Skapa och hantera anspråksbara virtuella datorer i Azure DevTest Labs
Du lägger till en anspråksbar virtuell dator i ett labb på ett liknande sätt som du [lägger till en vanlig virtuell dator](devtest-lab-add-vm.md) – från en *bas* som antingen är en [anpassad avbildning,](devtest-lab-create-template.md) [formel](devtest-lab-manage-formulas.md)eller [Marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Den här självstudien går igenom med azure-portalen för att lägga till en anspråksbar virtuell dator i ett labb i DevTest Labs och visar de processer som en användare följer för att göra anspråk på och ta inte emot den virtuella datorn.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Steg för att lägga till en anspråksbar virtuell dator i ett labb i Azure DevTest Labs
1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i avsnittet **DEVOPS.** Om du väljer * (stjärna) **bredvid DevTest Labs** i **DEVOPS-avsnittet.** Den här åtgärden lägger till **DevTest Labs** i menyn till vänster så att du enkelt kan komma åt den nästa gång. Sedan kan du välja **DevTest Labs** på menyn till vänster.

    ![Alla tjänster - välj DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Välj det labb där du vill skapa den virtuella datorn i listan över labb.
2. På sidan **Översikt** på labbet väljer du **+ Lägg till**.

    ![Knappen Lägg till virtuell dator](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. På sidan **Välj en bas** väljer du en marknadsplatsavbildning för den virtuella datorn.
1. Gör följande åtgärder på fliken **Grundläggande inställningar** på sidan **Virtuell dator:**
    1. Ange ett namn för den virtuella datorn i textrutan **Virtuell datornamn.** Textrutan är förfylld för dig med ett unikt automatiskt genererat namn. Namnet motsvarar användarnamnet i din e-postadress följt av ett unikt tresiffrigt nummer. Den här funktionen sparar tid att tänka på ett datornamn och skriva det varje gång du skapar en maskin. Du kan åsidosätta det här autofyllda fältet med ett valfritt namn om du vill. Om du vill åsidosätta det automatiskt ifyllda namnet för den virtuella datorn anger du ett namn i textrutan **Virtuell datornamn.**
    2. Ange ett **användarnamn** som beviljas administratörsbehörighet på den virtuella datorn. Användarens **namn** för maskinen är förifyllt med ett unikt automatiskt genererat namn. Namnet motsvarar användarnamnet i din e-postadress. Den här funktionen sparar tid att bestämma dig för ett användarnamn varje gång du skapar en ny dator. Återigen kan du åsidosätta det här autofyllda fältet med ett användarnamn som du väljer om du vill. Om du vill åsidosätta det automatiskt ifyllda värdet för användarnamn anger du ett värde i textrutan **Användarnamn.** Den här **administrator** användaren beviljas administratörsbehörighet på den virtuella datorn.
    3. Om du skapar den första virtuella datorn i labbet anger du ett **lösenord** för användaren. Om du vill spara det här lösenordet som standardlösenord i Azure-nyckelvalvet som är associerat med labbet väljer du **Spara som standardlösenord**. Standardlösenordet sparas i nyckelvalvet med namnet: **VmPassword**. När du försöker skapa efterföljande virtuella datorer i labbet väljs **VmPassword** automatiskt för **lösenordet**. Om du vill åsidosätta värdet avmarkerar du kryssrutan **Använd en sparad hemlighet** och anger ett lösenord.

        Du kan också spara hemligheter i nyckelvalvet först och sedan använda den när du skapar en virtuell dator i labbet. Mer information finns [i Lagra hemligheter i ett nyckelvalv](devtest-lab-store-secrets-in-key-vault.md). Om du vill använda lösenordet som lagras i nyckelvalvet väljer du **Använd en sparad hemlighet**och anger ett nyckelvärde som motsvarar ditt hemliga (lösenord).
    4. I avsnittet **Fler alternativ** väljer du **Ändra storlek**. Välj ett av de fördefinierade objekt som anger processorkärnorna, RAM-storleken och hårddiskstorleken på den virtuella datorn som ska skapas.
    5. Välj **Lägg till eller ta bort artefakter**. Markera och konfigurera de artefakter som du vill lägga till i basavbildningen.
    **Anm.:** Om du inte har tidigare till DevTest Labs eller konfigurera artefakter läser du avsnittet [Lägg till en befintlig artefakt till en virtuell dator](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) och returnerar sedan hit när du är klar.
2. Växla till fliken **Avancerade inställningar** högst upp och gör följande:
    1. Om du vill ändra det virtuella nätverket som den virtuella datorn finns i väljer du **Ändra VNet**.
    2. Om du vill ändra undernätet väljer du **Ändra undernät**.
    3. Ange om IP-adressen för den virtuella datorn är **offentlig, privat eller delad**.
    4. Om du vill ta bort den virtuella datorn automatiskt anger du **utgångsdatum och tid**.
    5. Om du vill göra den virtuella datorn anspråksbar av en labbanvändare väljer du **Ja** för **Gör det här alternativet för anspråksbara dator.**
    6. Ange antalet **instanser av virtuell dator** som du vill göra den tillgänglig för dina labbanvändare.
3. Välj **Skapa** om du vill lägga till den angivna virtuella datorn i labbet.

   Labbsidan visar status för den virtuella datorns skapande - först som **Skapa**, sedan som **körs** efter att den virtuella datorn har startats.

> [!NOTE]
> Om du distribuerar virtuella labb-datorer via [Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)kan du skapa anspråksbara virtuella datorer genom att ange egenskapen **allowClaim** till true i egenskapsavsnittet.


## <a name="using-a-claimable-vm"></a>Använda en dator som kan begäras på begäran

En användare kan göra anspråk på alla virtuella datorer från listan över "Anspråksbara virtuella datorer" genom att göra något av följande steg:

* Högerklicka på en av de virtuella datorerna i listan i listan över "Anspråksbara virtuella datorer" längst ned i labbets "Översikt"-fönster och högerklicka på en av de virtuella datorerna i listan och välj **Anspråksmaskin**.

  ![Begär en specifik anspråksbar virtuell dator.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Högst upp i fönstret Översikt väljer du **Gör anspråk på valfri**. En slumpmässig virtuell dator tilldelas från listan över anspråksbara virtuella datorer.

  ![Begär alla anspråksbara virtuella datorer.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


När en användare har gjort anspråk på en virtuell dator startar DevTest Labs datorn och flyttar upp den till labbanvändarens lista över "Mina virtuella datorer". Det innebär att labbanvändaren nu har ägarbehörighet på den här datorn. Den tid som krävs för det här steget kan variera beroende på starttider samt andra anpassade åtgärder som utförs under anspråkshändelsen. När den har begärts är maskinen inte längre tillgänglig i den anspråksbara poolen.  

## <a name="unclaim-a-vm"></a>Unclaim en VM

När en användare är klar med en påstådd virtuell dator och vill göra den tillgänglig för någon annan, kan de returnera den påstådda virtuella datorn till listan över anspråksbara virtuella datorer genom att göra något av följande steg:

- Från listan över "Mina virtuella datorer" högerklickar du på en av de virtuella datorerna i listan – eller väljer dess ellips (...) – och väljer **Unclaim**.

  ![Unclaim en virtuell dator på listan över vm.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- I listan "Mina virtuella datorer" väljer du en virtuell dator för att öppna hanteringsfönstret och väljer sedan **Unclaim** i det övre menyraden.

  ![Ta upp en virtuell dator i hanteringsfönstret för den virtuella datorn.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

När en användare inte kräver en virtuell dator har de inte längre ägarbehörighet för den specifika virtuella labbet och det är tillgängligt att begäras av alla andra labbanvändare i det tillstånd som den har återförts till poolen. 

### <a name="transferring-the-data-disk"></a>Överföra datadisken
Om en anspråksbar virtuell dator har en datadisk kopplad till sig och en användare inte tar emot den, stannar datadisken med den virtuella datorn. När en annan användare sedan hävdar att den virtuella datorn, den nya användaren hävdar datadisken samt den virtuella datorn.

Detta kallas "överföra datadisken". Datadisken blir sedan tillgänglig i den nya användarens lista över **Mina datadiskar** som de kan hantera.

![Ta inte upp datadiskar.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Nästa steg
* När den har skapats kan du ansluta till den virtuella datorn genom att välja **Anslut** i hanteringsfönstret.
* Utforska [snabbstartsgalleriet DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
