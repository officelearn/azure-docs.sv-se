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
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: fdffa3862f45b99c2c3f2ed41934e09247808ca7
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150055"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Skapa och hantera tillgängliga virtuella datorer i Azure DevTest Labs
Du lägger till en virtuell dator till ett labb i liknande sätt som hur du [lägga till en standard virtuell dator](devtest-lab-add-vm.md) – från en *grundläggande* som är antingen en [anpassad avbildning](devtest-lab-create-template.md), [formeln](devtest-lab-manage-formulas.md) , eller [Marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Den här självstudiekursen beskriver hur du använder Azure-portalen för att lägga till en virtuell dator i ett labb i DevTest Labs och visar processerna som en användare följer för att begära och avsäga sig den virtuella datorn.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Hur du lägger till en virtuell dator till ett labb i Azure DevTest Labs
1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i den **DEVOPS** avsnittet. Om du väljer * (star) bredvid **DevTest Labs** i den **DEVOPS** avsnittet. Den här åtgärden lägger till **DevTest Labs** till menyn till vänster navigering så att du enkelt kan nå nästa gång. Välj sedan **DevTest Labs** på menyn till vänster navigering.

    ![Alla tjänster – Välj DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. I listan över labbar Välj labb där du vill skapa den virtuella datorn.
2. På testmiljön **översikt** väljer **+ Lägg till**.

    ![Lägg till VM-knapp](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. På den **väljer bas** väljer en marketplace-avbildning för den virtuella datorn.
1. På den **grundinställningar** fliken den **VM** gör du följande åtgärder:
    1. Ange ett namn för den virtuella datorn i den **virtuellt datornamn** textrutan. Textrutan är förifylld åt dig med ett unikt namn för automatiskt genererade. Namnet motsvarar användarnamnet i din e-postadress som du har följt av ett unikt nummer 3 siffror. Den här funktionen sparar du tid att tänka på ett namn för datorn och ange det varje gång du skapar en dator. Du kan åsidosätta fältet fylls i automatiskt med ett valfritt namn om du vill. Om du vill åsidosätta det automatisk ifyllda namnet för den virtuella datorn, anger du ett namn i den **virtuellt datornamn** textrutan.
    2. Ange en **användarnamn** som har beviljats administratörsbehörighet på den virtuella datorn. Den **användarnamn** för datorn är förifyllda med ett unikt namn för automatiskt genererade. Namnet motsvarar användarnamnet i din e-postadress. Den här funktionen sparar du tid att besluta om ett användarnamn varje gång du skapar en ny dator. Igen, kan du åsidosätta fältet fylls i automatiskt med ett användarnamn för ditt val om du vill. Om du vill åsidosätta det automatisk ifyllda värdet för användarnamn, ange ett värde i den **användarnamn** textrutan. Den här användaren beviljas **administratör** behörighet på den virtuella datorn.
    3. Om du skapar första virtuella dator i labbet, anger du en **lösenord** för användaren. Spara lösenordet som standardlösenord i Azure key vault som är associerade med labbet, välja **Spara som standardlösenord**. Standardlösenordet sparas i nyckelvalvet med namnet: **VmPassword**. När du försöker skapa efterföljande virtuella datorer i labbet, **VmPassword** väljs automatiskt för den **lösenord**. Om du vill åsidosätta värdet, avmarkera de **använder en sparad hemlighet** kryssrutan och ange ett lösenord.

        Du kan också spara hemligheter i nyckelvalvet först och sedan använda den när du skapar en virtuell dator i labbet. Mer information finns i [Store hemligheter i key vault](devtest-lab-store-secrets-in-key-vault.md). Om du vill använda lösenord som lagras i nyckelvalvet, Välj **använder en sparad hemlighet**, och ange ett nyckelvärde som motsvarar din hemlighet (lösenord).
    4. I den **fler alternativ** väljer **ändra storleken på**. Välj en av de fördefinierade objekt som anger processorkärnor, RAM-storleken och storleken på hårddisken på den virtuella datorn för att skapa.
    5. Välj **lägga till eller ta bort artefakter**. Välj och konfigurera de artefakter som du vill lägga till i basavbildningen.
    **Obs!** Om du är nybörjare på DevTest Labs eller konfigurera artefakter, referera till den [lägga till en befintlig artefakt i en virtuell dator](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) avsnittet och återgå sedan här när du är klar.
2. Växla till den **avancerade inställningar** fliken högst upp och göra följande:
    1. Om du vill ändra det virtuella nätverk som Virtuellt datorn, Välj **ändra VNet**.
    2. Om du vill ändra undernätet, Välj **ändra undernätet**.
    3. Ange om IP-adressen för den virtuella datorn är **offentliga, privata eller delade**.
    4. Ta automatiskt bort den virtuella datorn genom att ange den **utgångsdatum och utgångstid**.
    5. Om du vill göra den virtuella datorn tillgängliga av en lab-användare, Välj **Ja** för **gör till virtuell dator** alternativet.
    6. Ange hur många av de **instanser av Virtuella** att du vill göra den tillgänglig för dina labbanvändare.
3. Välj **skapa** att lägga till den angivna virtuella datorn till labbet.

   Sidan labb visar status för den Virtuella datorns Skapa - först som **skapa**, sedan som **kör** när den virtuella datorn har startats.

> [!NOTE]
> Om du distribuerar labbet virtuella datorer via [Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md), du kan skapa tillgängliga virtuella datorer genom att ange den **allowClaim** egenskapen på Sant i egenskapsavsnittet.


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
* Utforska den [DevTest Labs Azure Resource Manager-mall snabbstartsgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
