---
title: Lägga till en virtuell dator i ett labb i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du lägger till en virtuell dator i ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 22060cc6dd5eb15e81a0c397a7b0255f16780d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284257"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Lägga till en virtuell dator i ett labb i Azure DevTest Labs
Om du redan har [skapat den första virtuella datorn](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)har du troligen gjort det från en förinläst [marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Nu, om du vill lägga till efterföljande virtuella datorer i labbet, kan du också välja en *bas* som är antingen en [anpassad bild](devtest-lab-create-template.md) eller en [formel](devtest-lab-manage-formulas.md). Den här självstudien går igenom med Azure-portalen för att lägga till en virtuell dator i ett labb i DevTest Labs.

Den här artikeln visar också hur du hanterar artefakter för en virtuell dator i labbet.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Steg för att lägga till en virtuell dator i ett labb i Azure DevTest Labs
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

        ![Välja en bas](./media/tutorial-create-custom-lab/new-virtual-machine.png)

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

        ![Välja en bas](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Välj **Skapa** om du vill lägga till den angivna virtuella datorn i labbet.

   Labbsidan visar status för den virtuella datorns skapande - först som **Skapa**, sedan som **körs** efter att den virtuella datorn har startats.

    ![Skapandestatus för virtuell dator](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Lägga till en befintlig artefakt till en virtuell dator
När du skapar en virtuell dator kan du lägga till befintliga artefakter. Varje labb innehåller artefakter från den offentliga DevTest Labs Artifact Repository samt artefakter som du har skapat och lagt till i din egen Artefakt repository.

* Med Azure DevTest *Labs-artefakter* kan du ange *åtgärder* som utförs när den virtuella datorn etableras, till exempel att köra Windows PowerShell-skript, köra Bash-kommandon och installera programvara.
* Med *artefaktparametrar* kan du anpassa artefakten för just det scenario

Information om hur du skapar artefakter finns i artikeln [Lär dig hur du skapar egna artefakter som du kan använda med DevTest Labs](devtest-lab-artifact-author.md).

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
1. I listan över labb väljer du det labb som innehåller den virtuella datorn som du vill arbeta med.
1. Välj **Mina virtuella datorer**.
1. Välj önskad virtuell dator.
1. Välj **Hantera artefakter**.
1. Välj **Använd artefakter**.
1. I fönstret **Använd artefakter** väljer du den artefakt som du vill lägga till i den virtuella datorn.
1. I fönstret **Lägg till artefakt** anger du de parametervärden som krävs och eventuella valfria parametrar som du behöver.
1. Välj **Lägg till** om du vill lägga till artefakten och återgå till fönstret Använd **artefakter.**
1. Fortsätt att lägga till artefakter efter behov för den virtuella datorn.
1. När du har lagt till dina artefakter kan du [ändra i vilken ordning artefakterna körs](#change-the-order-in-which-artifacts-are-run). Du kan också gå tillbaka för att [visa eller ändra en artefakt](#view-or-modify-an-artifact).
1. När du är klar med att lägga till artefakter väljer du **Använd**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Ändra i vilken ordning artefakter körs
Som standard utförs åtgärderna för artefakterna i den ordning som de läggs till i den virtuella datorn.
Följande steg illustrerar hur du ändrar i vilken ordning artefakterna körs.

1. Högst upp i fönstret **Använd artefakter** väljer du länken som anger antalet artefakter som har lagts till i den virtuella datorn.

    ![Antal artefakter som lagts till i den virtuella datorn](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Dra och släppa artefakterna i önskad ordning i fönstret **Markerade artefakter.** **Anm.:** Om du har problem med att dra artefakten kontrollerar du att du drar från artefaktens vänstra sida.
1. Välj **OK** när du är klar.

## <a name="view-or-modify-an-artifact"></a>Visa eller ändra en artefakt
Följande steg illustrerar hur du visar eller ändrar parametrarna för en artefakt:

1. Högst upp i fönstret **Använd artefakter** väljer du länken som anger antalet artefakter som har lagts till i den virtuella datorn.

    ![Antal artefakter som lagts till i den virtuella datorn](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Markera den artefakt som du vill visa eller redigera i fönstret **Markerade artefakter.**
1. I fönstret **Lägg till artefakt** gör du nödvändiga ändringar och väljer **OK** för att stänga fönstret Lägg **till artefakt.**
1. Välj **OK** om du vill stänga fönstret **Markerade artefakter.**

## <a name="save-azure-resource-manager-template"></a>Spara Azure Resource Manager-mall
En Azure Resource Manager-mall är ett deklarativt sätt att definiera en repeterbar distribution.
I följande steg beskrivs hur du sparar Azure Resource Manager-mallen för den virtuella datorn som skapas.
När du har sparat kan du använda Azure Resource Manager-mallen för att [distribuera nya virtuella datorer med Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. I fönstret **Virtuell dator** väljer du Visa Azure **Resource Manager-mall**.
2. Markera malltexten i **mallfönstret Visa Azure Resource Manager.**
3. Kopiera den markerade texten till Urklipp.
4. Välj **OK** om du vill stänga **mallfönstret Visa Azure Resource Manager**.
5. Öppna en textredigerare.
6. Klistra in malltexten från Urklipp.
7. Spara filen för senare användning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* När den virtuella datorn har skapats kan du ansluta till den virtuella datorn genom att välja **Anslut** på den virtuella datorns fönsterruta.
* Lär dig hur du [skapar anpassade artefakter för din DevTest Labs VM](devtest-lab-artifact-author.md).
* Utforska [snabbstartsgalleriet DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
