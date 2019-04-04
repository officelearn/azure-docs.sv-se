---
title: Ange obligatoriska artefakter för din Azure DevTest Labs | Microsoft Docs
description: Lär dig mer om att ange obligatoriska artefakter som måste installeras innan du installerar alla användarvalda artefakter på virtuella datorer (VM) i laboratoriet.
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
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905808"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Ange obligatoriska artefakter för ditt labb i Azure DevTest Labs
Ägare av ett labb kan ange du obligatoriska artefakter som tillämpas på varje dator som har skapats i laboratoriet. Tänk dig ett scenario där du vill att varje dator i labbet vara anslutna till företagsnätverket. Varje labb-användaren behöver i det här fallet lägger du till en domän join-artefakt vid skapande av virtuella datorer att kontrollera att deras dator är ansluten till företagsdomänen. Med andra ord behöver labbanvändare i stort sett skapa en virtuell dator om de glömmer att tillämpa obligatoriska artefakter på sin dator. Som labbägare kan göra du den domän join-artefakten som en obligatorisk artefakt i labbet. Det här steget säkerställer att varje dator är ansluten till företagsnätverket och spara tid och arbete för dina labbanvändare.
 
Andra obligatoriska artefakter kan omfatta ett vanliga verktyg som använder ditt team eller en plattformsrelaterade security-paket som krävs för varje dator har som standard osv. Kort sagt så blir eventuella vanliga program som alla virtuella datorer i labbet måste ha en obligatorisk artefakt. Om du skapar en anpassad avbildning från en dator som har obligatoriska artefakter som tillämpas på den och sedan skapa en ny dator från avbildningen, obligatoriska artefakter tillämpas på nytt på datorn när du skapar. Detta innebär också som trots att den anpassade avbildningen är gammal varje gång du skapar en virtuell dator från den mest uppdaterade versionen av obligatoriska artefakter tillämpas på det vid skapa flödet. 
 
Artefakter som saknar parametrar stöds som obligatoriska som. Lab-användare behöver inte ange ytterligare parametrar under lab skapande och därför kan du förenkla processen att skapa en virtuell dator. 

## <a name="specify-mandatory-artifacts"></a>Ange obligatoriska artefakter
Du kan välja obligatoriska artefakter för Windows och Linux-datorer separat. Du kan också ändra ordningen på dessa artefakter beroende på den ordning i vilken du vill att de tillämpas. 

1. På startsidan för labbet, Välj **konfiguration och principer** under **inställningar**. 
3. Välj **obligatoriska artefakter** under **externa resurser**. 
4. Välj **redigera** i den **Windows** avsnittet eller **Linux** avsnittet. Det här exemplet används den **Windows** alternativet. 

    ![Obligatorisk artefakter sidan – Redigera-knappen](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Välj en artefakt. Det här exemplet används **7 Zip** alternativet. 
5. På den **Lägg till artefakt** väljer **Lägg till**. 

    ![Obligatorisk artefakter sidan – lägga till 7 zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Markera artikeln om du vill lägga till en annan artefakt, och välj **Lägg till**. Det här exemplet lägger till **Chrome** som andra obligatoriska artefakten.

    ![Obligatorisk artefakter sidan – lägga till Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. På den **obligatoriska artefakter** kan du se ett meddelande som anger hur många artefakter som valts. Om du klickar på meddelandet visas artefakter som du har valt. Välj **spara** att spara. 

    ![Obligatorisk artefakter sidan – spara artefakter](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Upprepa stegen för att ange obligatoriska artefakter för virtuella Linux-datorer. 
    
    ![Obligatorisk artefakter sidan – Windows- och Linux-artefakter](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Att **ta bort** en artefakt i listan, Välj **... (tre punkter)**  i slutet av raden och välj **ta bort**. 
10. Att **ordna om** artefakter i listan, hovrar musen över artefakten, Välj **... (tre punkter)**  som visas i början av raden och dra objektet till den nya platsen. 
11. Välj för att spara obligatoriska artefakter i laboratoriet **spara**. 

    ![Obligatorisk artefakter sidan – spara artefakter i labb](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Stäng den **konfiguration och principer** sidan (Markera **X** i det övre högra hörnet) att gå tillbaka till startsidan för labbet.  

## <a name="delete-a-mandatory-artifact"></a>Ta bort en obligatorisk artefakt
Om du vill ta bort en obligatorisk artefakt från ett labb, gör du följande åtgärder: 

1. Välj **konfiguration och principer** under **inställningar**. 
2. Välj **obligatoriska artefakter** under **externa resurser**. 
3. Välj **redigera** i den **Windows** avsnittet eller **Linux** avsnittet. Det här exemplet används den **Windows** alternativet. 
4. Välj meddelandet med antalet obligatoriska artefakter högst upp. 

    ![Obligatorisk artefakter sidan – Välj meddelandet](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. På den **valt artefakter** väljer **... (tre punkter)**  för artefakten ska tas bort och välj **ta bort**. 
    
    ![Obligatorisk artefakter sidan – ta bort artefakt](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Välj **OK** att Stäng den **valt artefakter** sidan. 
7. Välj **spara** på den **obligatoriska artefakter** sidan.
8. Upprepa steg för **Linux** avbildningar om det behövs. 
9. Välj **spara** att spara alla ändringar till labbet. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Visa obligatoriska artefakter när du skapar en virtuell dator
Nu som en lab-användare kan visa listan över obligatoriska artefakter när du skapar en virtuell dator i labbet. Du kan inte redigera eller ta bort obligatoriska artefakter i laboratoriet som din labbägare.

1. På startsidan för labbet, väljer **översikt** på menyn.
2. Om du vill lägga till en virtuell dator i labbet, Välj **+ Lägg till**. 
3. Välj en **basavbildningen**. Det här exemplet används **Windows Server, version 1709**.
4. Observera att du ser ett meddelande för **artefakter** med antal obligatoriska artefakter som valts. 
5. Välj **artefakter**. 
6. Kontrollera att du ser den **obligatoriska artefakter** du angav i testmiljön konfiguration och principer. 

    ![Skapa en virtuell dator – obligatoriska artefakter](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägga till en Git-lagringsplats för artefakter i ett labb](devtest-lab-add-artifact-repo.md).

