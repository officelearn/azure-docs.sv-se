---
title: Lägg till en virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en virtuell dator till ett labb i Azure DevTest Labs
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
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: e86568da7f3d607c90e42e09a61ced9993c4d744
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254051"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Lägg till en virtuell dator i ett labb i Azure DevTest Labs
Om du redan har [skapat din första virtuella dator](devtest-lab-create-first-vm.md), du sannolikt gjorde det från en förinstallerade [marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Nu, om du vill lägga till efterföljande virtuella datorer i labbet, du kan också välja en *grundläggande* som är antingen en [anpassad avbildning](devtest-lab-create-template.md) eller en [formeln](devtest-lab-manage-formulas.md). Den här självstudiekursen beskriver hur du använder Azure-portalen att lägga till en virtuell dator i ett labb i DevTest Labs.

Den här artikeln beskriver också hur du hanterar artefakter för en virtuell dator i labbet.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Hur du lägger till en virtuell dator till ett labb i Azure DevTest Labs
1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. I listan över labbar Välj labb där du vill skapa den virtuella datorn.  
1. På testmiljön **översikt** väljer **+ Lägg till**.  

    ![Lägg till VM-knapp](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. På den **väljer bas** fönstret väljer du en bas för den virtuella datorn.
1. På den **VM** fönstret **virtuellt datornamn** ifyllda åt dig med ett unikt namn för automatiskt genererade. Namnet motsvarar användarnamnet i din e-postadress som du har följt av ett unikt nummer 3 siffror. Den här funktionen sparar du tid att tänka på ett namn för datorn och ange det varje gång du skapar en dator. Du kan åsidosätta fältet fylls i automatiskt med ett valfritt namn om du vill. Om du vill åsidosätta det automatisk ifyllda namnet för den virtuella datorn, anger du ett namn i den **virtuellt datornamn** textrutan. 

    ![Lab VM-fönstret](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Den **användarnamn** för datorn är förifyllda med ett unikt namn för automatiskt genererade. Namnet motsvarar användarnamnet i din e-postadress. Den här funktionen sparar du tid att besluta om ett användarnamn varje gång du skapar en ny dator. Igen, kan du åsidosätta fältet fylls i automatiskt med ett användarnamn för ditt val om du vill. Om du vill åsidosätta det automatisk ifyllda värdet för användarnamn, ange ett värde i den **användarnamn** textrutan. Den här användaren beviljas **administratör** behörighet på den virtuella datorn.     
1. För den **lösenord**:
    
    Om du skapar första virtuella dator i labbet, ange ett lösenord i den **skriver ett värde** textrutan. Spara lösenordet som standardlösenord i Azure key vault som är associerade med labbet, välja **Spara som standardlösenord**. Standardlösenordet sparas i nyckelvalvet med namnet: **VmPassword**. När du försöker skapa efterföljande virtuella datorer i labbet, **VmPassword** väljs automatiskt för den **lösenord**. Om du vill åsidosätta värdet, avmarkera de **använder en sparad hemlighet** kryssrutan och ange ett lösenord. 

    Du kan också spara hemligheter i nyckelvalvet först och sedan använda den när du skapar en virtuell dator i labbet. Mer information finns i [Store hemligheter i key vault](devtest-lab-store-secrets-in-key-vault.md). Om du vill använda lösenord som lagras i nyckelvalvet, Välj **använder en sparad hemlighet**, och ange ett nyckelvärde som motsvarar din hemlighet (lösenord). 
3. Den **disktyp för virtuell dator** avgör vilken typ av premiumlagring tillåts för virtuella datorer i labbet.
4. Välj **VM-storlek** och välj en av de fördefinierade objekt som anger processorkärnor, RAM-storleken och storleken på hårddisken på den virtuella datorn för att skapa.
5. Välj **artefakter** och - från listan över artefakter - Välj och konfigurera de artefakter som du vill lägga till i basavbildningen.
    **Obs:** om du är nybörjare på DevTest Labs eller konfigurera artefakter, referera till den [lägga till en befintlig artefakt i en virtuell dator](#add-an-existing-artifact-to-a-vm) avsnittet och återgå sedan här när du är klar.
6. Välj **avancerade inställningar** att konfigurera Virtuellt datorns Nätverksalternativ och alternativ för upphör att gälla. 

   Om du vill ange ett alternativ för upphör att gälla, väljer du på kalenderikonen att ange ett datum som den virtuella datorn tas bort automatiskt.  Som standard upphör aldrig att den virtuella datorn. 
1. Om du vill visa eller kopiera Azure Resource Manager-mallen finns i [spara Azure Resource Manager-mall](#save-azure-resource-manager-template) avsnitt och komma tillbaka hit när du är klar.
1. Välj **skapa** att lägga till den angivna virtuella datorn till labbet.
1. Fönstret labb visar status för den Virtuella datorns Skapa - först som **skapa**, sedan som **kör** när den virtuella datorn har startats.

> [!NOTE]
> [Lägg till en virtuell dator som](devtest-lab-add-claimable-vm.md) visar hur du gör den virtuella datorn ska vara tillgängliga så att den är tillgänglig för användning av någon användare i laboratoriet.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Lägg till en befintlig artefakt i en virtuell dator
När du skapar en virtuell dator måste du lägga till befintliga artefakter. Varje labb innehåller artefakter från offentliga DevTest Labs-Artefaktcentrallagret samt artefakter som du har skapat och lagts till i din egen Artefaktcentrallagret.

* Azure DevTest Labs *artefakter* kan du ange *åtgärder* som utförs när den virtuella datorn etableras, t.ex köra Windows PowerShell-skript, köra Bash-kommandon och installera programvara.
* Artefakten *parametrar* kan du anpassa artefakten för ditt specifika scenario

Identifiera hur du skapar artefakter, finns i artikeln [om hur du skapar dina egna artefakter för användning med DevTest Labs](devtest-lab-artifact-author.md).

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. Välj labbet som innehåller den virtuella datorn som du vill arbeta i listan över labbar.  
1. Välj **Mina virtuella datorer**.
1. Välj den virtuella datorn.
1. Välj **hantera artefakter**. 
1. Välj **tillämpa artefakter**.
1. På den **tillämpa artefakter** fönstret, Välj den artefakt som du vill lägga till till den virtuella datorn.
1. På den **Lägg till artefakt** fönstret anger du de obligatoriska parametervärdena och parametrar som du behöver.  
1. Välj **Lägg till** att lägga till artefakten och återgå till den **tillämpa artefakter** fönstret.
1. Fortsätt lägga till artefakter som behövs för den virtuella datorn.
1. När du har lagt till artefakterna för, kan du [ändra ordning där artefakter körs](#change-the-order-in-which-artifacts-are-run). Du kan också gå tillbaka till [visa eller ändra en artefakt](#view-or-modify-an-artifact).
1. När du är klar att lägga till artefakter, Välj **tillämpa**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Ändra ordning där artefakter körs
Som standard utförs åtgärderna för artefakter i den ordning som de läggs till den virtuella datorn. Följande steg illustrerar hur du ändrar den ordning i vilken artefakterna körs.

1. Överst på den **tillämpa artefakter** fönstret, klicka på länken som anger antalet artefakter som har lagts till den virtuella datorn.
   
    ![Antal artefakter som lagts till i virtuell dator](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. På den **valt artefakter** fönstret dra och släpp artefakter i önskad ordning. **Obs:** om du har problem med att dra artefakten, se till att du drar från vänster sida av artefakten. 
1. Välj **OK** när du är klar.  

## <a name="view-or-modify-an-artifact"></a>Visa eller ändra en artefakt
Följande steg illustrerar hur du vill visa eller ändra parametrarna för en artefakt:

1. Överst på den **tillämpa artefakter** fönstret, klicka på länken som anger antalet artefakter som har lagts till den virtuella datorn.
   
    ![Antal artefakter som lagts till i virtuell dator](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. På den **valt artefakter** fönstret, Välj den artefakt som du vill visa eller redigera.  
1. På den **Lägg till artefakt** fönstret, se något behov ändringar och välj **OK** att Stäng den **Lägg till artefakt** fönstret.
1. Välj **OK** att Stäng den **valt artefakter** fönstret.

## <a name="save-azure-resource-manager-template"></a>Spara Azure Resource Manager-mall
En Azure Resource Manager-mall innehåller en deklarativ metod för att definiera en upprepningsbara distributioner. Följande steg beskriver hur du sparar Azure Resource Manager-mallen för den virtuella datorn håller på att skapas.
När du har sparat kan du använda Azure Resource Manager-mall till [distribuera nya virtuella datorer med Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. På den **VM** väljer **visa Azure Resource Manager-mall**.
2. På den **visa Azure Resource Manager-mall** fönstret Välj mallens text.
3. Kopiera den markerade texten till Urklipp.
4. Välj **OK** att Stäng den **visa Azure Resource Manager-mall fönstret**.
5. Öppna en textredigerare.
6. Klistra in mallens text från Urklipp.
7. Spara filen för senare användning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* När den virtuella datorn har skapats kan du ansluta till den virtuella datorn genom att välja **Connect** i fönstret för den virtuella datorn.
* Lär dig hur du [skapa anpassade artefakter för dina DevTest Labs VM](devtest-lab-artifact-author.md).
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
