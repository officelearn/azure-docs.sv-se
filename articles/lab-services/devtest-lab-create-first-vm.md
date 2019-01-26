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
ms.openlocfilehash: c72e10991e27f7d616703e635ee6e1a18122afc5
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078084"
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Skapa din första virtuella dator i ett labb i Azure DevTest Labs

När du först få åtkomst till labb och vill skapa din första virtuella dator, du kommer förmodligen göra det med hjälp av en förinstallerade [grundläggande marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Vid ett senare tillfälle du kommer även att kunna välja från en [anpassad avbildning och en formel](devtest-lab-add-vm.md) när du skapar fler virtuella datorer. 

Den här självstudiekursen beskriver hur du använder Azure-portalen att lägga till din första virtuella dator till ett labb i DevTest Labs.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Hur du lägger till din första virtuella dator till ett labb i Azure DevTest Labs
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

## <a name="next-steps"></a>Nästa steg
* När den virtuella datorn har skapats kan du ansluta till den virtuella datorn genom att välja **Connect** på sidan för den virtuella datorn.
* Kolla in [lägga till en virtuell dator i en labb](devtest-lab-add-vm.md) fullständig information om att lägga till efterföljande virtuella datorer i labbet.
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
