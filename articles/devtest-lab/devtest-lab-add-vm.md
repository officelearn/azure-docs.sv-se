---
title: "Lägga till en virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs"
description: "Lär dig hur du lägger till en virtuell dator i ett labb i Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
ms.openlocfilehash: 449bffb040dafc8edd0b8b0afd80dbea35cd28ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Lägga till en virtuell dator i ett labb i Azure DevTest Labs
Om du redan har [skapat din första virtuella dator](devtest-lab-create-first-vm.md), du förmodligen gjorde det från en förinstallerade [marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Nu om du vill lägga till följande virtuella datorer i labbet, du kan också välja en *grundläggande* som är antingen en [anpassad avbildning](devtest-lab-create-template.md) eller en [formeln](devtest-lab-manage-formulas.md). Den här självstudiekursen vägleder dig genom att använda Azure portal för att lägga till en virtuell dator i ett labb i DevTest Labs.

Den här artikeln visar även hur du hanterar artefakter för en virtuell dator i labbet.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Steg för att lägga till en virtuell dator i ett labb i Azure DevTest Labs
1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
1. Välj labbet där du vill skapa den virtuella datorn från listan över övningar.  
1. På testmiljön **översikt** bladet väljer **+ Lägg till**.  

    ![VM-knappen Lägg till](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. På den **väljer du en bas** bladet Välj en bas för den virtuella datorn.
1. På den **virtuella** bladet anger du ett namn för den nya virtuella datorn i den **virtuellt datornamn** textruta.

    ![Blad för labbet VM](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Ange en **användarnamn** som har beviljats administratörsbehörighet på den virtuella datorn.  
1. Om du vill använda ett lösenord som lagras i din [hemliga store](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)väljer **använda en sparad hemlighet**, och ange ett nyckelvärde som motsvarar ditt hemliga (lösenord). Annars kan ange ett lösenord i fältet med etiketten **skriver ett värde**.
1. Den **virtuella disktyp** avgör vilken lagringstyp som disk är tillåten för virtuella datorer i labbet.
1. Välj **storlek för virtuell dator** och välj en av de fördefinierade objekt som anger processorkärnor, ram-storlek och storleken på hårddisken på den virtuella datorn för att skapa.
1. Välj **artefakter** och - från listan över artefakter - Välj och konfigurera artefakter som du vill lägga till basavbildningen.
    **Obs:** om du inte har arbetat med DevTest Labs eller konfigurera artefakter, referera till den [lägga till en befintlig artefakt till en virtuell dator](#add-an-existing-artifact-to-a-vm) avsnittet och sedan återvända hit när du är klar.
1. Välj **avancerade inställningar** att konfigurera Virtuellt Nätverksalternativ och upphör att gälla. 

   Välj kalenderikonen för att ange ett datum då den virtuella datorn tas bort automatiskt om du vill ange ett förfallodatum-alternativ.  Som standard upphör aldrig den virtuella datorn. 
1. Om du vill visa eller kopiera Azure Resource Manager-mallen finns i [spara Azure Resource Manager-mall](#save-azure-resource-manager-template) avsnittet och återvända hit när du är klar.
1. Välj **skapa** att lägga till den angivna virtuella datorn i labbet.
1. Blad för labbet visar status för den virtuella datorn skapas - först som **skapa**, sedan som **kör** när den virtuella datorn har startats.

> [!NOTE]
> [Lägg till en claimable VM](devtest-lab-add-claimable-vm.md) visar hur du gör den virtuella datorn claimable så att den är tillgänglig för användning av alla användare i labbet.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Lägg till en befintlig artefakt till en virtuell dator
När du skapar en virtuell dator måste du lägga till befintliga artefakter. Varje labb ingår artefakter från offentliga DevTest Labs Artefaktcentrallagret samt artefakter som du skapat och lägga till egna Artefaktcentrallagret.

* Azure DevTest Labs *artefakter* kan du ange *åtgärder* som utförs när den virtuella datorn har etablerats som kör Windows PowerShell-skript, köra Bash-kommandon och installera programvara.
* Artefakt *parametrar* kan du anpassa artefakten för ditt specifika scenario

Identifiera hur du skapar artefakter finns i artikeln [Lär dig hur du skapar egna artefakter för användning med DevTest Labs](devtest-lab-artifact-author.md).

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
1. Välj labb som innehåller den virtuella datorn som du vill arbeta i listan över övningar.  
1. Välj **Mina virtuella datorer**.
1. Välj den önskade virtuella datorn.
1. Välj **artefakter**. 
1. Välj **gäller artefakter**.
1. På den **gäller artefakter** bladet välj artefakt som du vill lägga till den virtuella datorn.
1. På den **Lägg till artefakt** bladet obligatorisk parameter-värden och valfria parametrar som du behöver.  
1. Välj **Lägg till** att lägga till artefakten och återgå till den **gäller artefakter** bladet.
1. Fortsätt lägga till artefakter som behövs för den virtuella datorn.
1. När du har lagt till din artefakter, kan du [ändra ordning i vilken artefakter körs](#change-the-order-in-which-artifacts-are-run). Du kan även gå tillbaka till [visa eller ändra en artefakt](#view-or-modify-an-artifact).
1. När du är klar att lägga till artefakter, Välj **tillämpa**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Ändra ordning i vilken artefakter körs
Som standard utförs åtgärderna för artefakter i den ordning som de läggs till den virtuella datorn. Följande steg visar hur du ändrar ordning där artefakter körs.

1. Längst upp i den **gäller artefakter** bladet Välj länken som anger antalet artefakter som har lagts till den virtuella datorn.
   
    ![Antal artefakter som lagts till VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. På den **valt artefakter** bladet dra och släpp artefakter i önskad ordning. **Obs:** om du har svårt att dra artefakten, se till att du drar från vänster sida av artefakten. 
1. Välj **OK** när du är klar.  

## <a name="view-or-modify-an-artifact"></a>Visa eller ändra en artefakt
Följande steg visar hur du visar eller ändrar parametrarna för en artefakt:

1. Längst upp i den **gäller artefakter** bladet Välj länken som anger antalet artefakter som har lagts till den virtuella datorn.
   
    ![Antal artefakter som lagts till VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. På den **valt artefakter** bladet välj artefakten som du vill visa eller redigera.  
1. På den **Lägg till artefakt** bladet, se något behov ändringar och välj **OK** att stänga den **Lägg till artefakt** bladet.
1. Välj **OK** att stänga den **valt artefakter** bladet.

## <a name="save-azure-resource-manager-template"></a>Spara Azure Resource Manager-mall
En mall för Azure Resource Manager tillhandahåller en deklarativ metod för att definiera en repeterbara distribution. Följande steg förklarar hur du sparar Azure Resource Manager-mallen för den virtuella datorn skapas.
När Spara, kan du använda Azure Resource Manager-mall till [distribuera nya virtuella datorer med Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. På den **virtuella** bladet väljer **visa ARM-mallen**.
2. På den **visa Azure Resource Manager-mall** bladet Välj mallens text.
3. Kopiera den markerade texten till Urklipp.
4. Välj **OK** att stänga den **visa Azure Resource Manager-mall bladet**.
5. Öppna en textredigerare.
6. Klistra in i mallens text från Urklipp.
7. Spara filen för senare användning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Nästa steg
* När den virtuella datorn har skapats kan du ansluta till den virtuella datorn genom att välja **Anslut** på bladet för den virtuella datorn.
* Lär dig hur du [skapa anpassade artefakter för den virtuella datorn med DevTest Labs](devtest-lab-artifact-author.md).
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
