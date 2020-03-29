---
title: Ange obligatoriska artefakter för dina Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du anger obligatoriska artefakter som måste installeras innan du installerar användarvalda artefakter på virtuella datorer i labbet.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 090236ec3647c7c3e38eb862780a615f854e952b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60562263"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Ange obligatoriska artefakter för ditt labb i Azure DevTest Labs
Som ägare av ett labb kan du ange obligatoriska artefakter som tillämpas på alla datorer som skapas i labbet. Föreställ dig ett scenario där du vill att varje dator i labbet ska vara ansluten till ditt företagsnätverk. I det här fallet måste varje labbanvändare lägga till en domänanslutningsartefakt när den virtuella datorn skapas för att se till att deras dator är ansluten till företagsdomänen. Med andra ord skulle labbanvändare i huvudsak måste återskapa en maskin om de glömmer att tillämpa obligatoriska artefakter på sin maskin. Som labbägare gör du domänens kopplingsartefakt som en obligatorisk artefakt i labbet. Det här steget ser till att varje dator är ansluten till företagets nätverk och sparar tid och ansträngning för dina labbanvändare.
 
Andra obligatoriska artefakter kan innehålla ett vanligt verktyg som ditt team använder, eller ett plattformsrelaterat säkerhetspaket som varje dator måste ha som standard etc. Kort sagt, alla vanliga program som varje maskin i ditt labb måste ha blir en obligatorisk artefakt. Om du skapar en anpassad avbildning från en dator som har obligatoriska artefakter som tillämpas på den och sedan skapar en ny dator från den avbildningen, tillämpas de obligatoriska artefakterna på datorn igen när de skapas. Detta innebär också att även om den anpassade avbildningen är gammal, tillämpas varje gång du skapar en dator från den den den mest uppdaterade versionen av obligatoriska artefakter på den under skapandeflödet. 
 
Endast artefakter som inte har några parametrar stöds som obligatoriska. Din labbanvändare behöver inte ange ytterligare parametrar när labbet skapas och därmed göra processen för att skapa virtuella datorer enkel. 

## <a name="specify-mandatory-artifacts"></a>Ange obligatoriska artefakter
Du kan välja obligatoriska artefakter för Windows- och Linux-datorer separat. Du kan också ändra ordning på dessa artefakter beroende på i vilken ordning du vill att de ska tillämpas. 

1. På labbets startsida väljer du **Konfiguration och principer** under **INSTÄLLNINGAR**. 
3. Välj **Obligatoriska artefakter** under EXTERNA **RESURSER**. 
4. Välj **Redigera** i avsnittet **Windows** eller i **avsnittet Linux.** I det här exemplet används alternativet **Windows.** 

    ![Sidan Obligatoriska artefakter - knappen Redigera](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Välj en artefakt. I det här exemplet används alternativet **7 zip.** 
5. På sidan **Lägg till artefakt** väljer du **Lägg till**. 

    ![Obligatorisk artefaktsida - Lägg till 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Om du vill lägga till en annan artefakt markerar du artikeln och väljer **Lägg till**. I det här exemplet **läggs Chrome** till som den andra obligatoriska artefakten.

    ![Obligatorisk sida för artefakter - Lägg till Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. På sidan **Obligatoriska artefakter** visas ett meddelande som anger antalet markerade artefakter. Om du klickar på meddelandet visas de artefakter som du har markerat. Välj **Spara** för att spara. 

    ![Obligatorisk artefaktsida - Spara artefakter](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Upprepa stegen för att ange obligatoriska artefakter för virtuella Linux-datorer. 
    
    ![Obligatorisk artefaktsida - Windows- och Linux-artefakter](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Om du vill **ta bort** en artefakt från listan väljer du **... (ellips)** i slutet av raden och välj **Ta bort**. 
10. Om du vill **ändra ordning** på artefakter i listan håller du muspekaren över artefakten och väljer **... (ellips)** som visas i början av raden och dra objektet till den nya positionen. 
11. Om du vill spara obligatoriska artefakter i labbet väljer du **Spara**. 

    ![Obligatorisk artefaktsida - Spara artefakter i labbet](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Stäng sidan **Konfiguration och principer** (välj **X** i det övre högra hörnet) för att komma tillbaka till startsidan för ditt labb.  

## <a name="delete-a-mandatory-artifact"></a>Ta bort en obligatorisk artefakt
Så här tar du bort en obligatorisk artefakt från ett labb: 

1. Välj **Konfiguration och principer** under **INSTÄLLNINGAR**. 
2. Välj **Obligatoriska artefakter** under EXTERNA **RESURSER**. 
3. Välj **Redigera** i avsnittet **Windows** eller i **avsnittet Linux.** I det här exemplet används alternativet **Windows.** 
4. Markera meddelandet med antalet obligatoriska artefakter högst upp. 

    ![Obligatorisk artefaktsida - Markera meddelandet](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. På sidan **Valda artefakter** väljer du **... (ellips)** för att artefakten ska tas bort och välj **Ta bort**. 
    
    ![Obligatorisk artefaktsida - Ta bort artefakt](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Välj **OK** om du vill stänga sidan **Markerade artefakter.** 
7. Välj **Spara** på sidan **Obligatoriska artefakter.**
8. Upprepa steg för **Linux-avbildningar** om det behövs. 
9. Välj **Spara** om du vill spara alla ändringar i labbet. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Visa obligatoriska artefakter när du skapar en virtuell dator
Nu kan du som labbanvändare visa listan över obligatoriska artefakter när du skapar en virtuell dator i labbet. Du kan inte redigera eller ta bort obligatoriska artefakter som anges i labbägaren.

1. På startsidan för ditt labb väljer du **Översikt** på menyn.
2. Om du vill lägga till en virtuell dator i labbet väljer du **+ Lägg till**. 
3. Välj en **basbild**. I det här exemplet används **Windows Server, version 1709**.
4. Observera att du ser ett meddelande för **artefakter** med antalet obligatoriska artefakter markerade. 
5. Välj **Artefakter**. 
6. Bekräfta att du ser de **obligatoriska artefakter som** du har angett i labbets konfiguration och principer. 

    ![Skapa en virtuell dator - obligatoriska artefakter](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägger till en Git-artefaktdatabas i ett labb](devtest-lab-add-artifact-repo.md).

