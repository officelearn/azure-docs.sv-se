---
title: Hantera formler i Azure DevTest Labs för att skapa virtuella datorer | Microsoft Docs
description: Lär dig att uppdatera och ta bort Azure DevTest Labs formler
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: d8f2ae64e2f8e694de5a7cf5aa9049e63998dca0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562601"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Hantera Azure DevTest Labs formler

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Den här artikeln visar hur du skapar en formel bas (anpassad avbildning, Marketplace-avbildning eller en annan metod) eller i en befintlig virtuell dator. Den här artikeln vägleder dig genom att hantera befintliga formler också.

## <a name="create-a-formula"></a>Skapa en formel
Vem som helst med DevTest Labs *användare* behörigheter kan skapa virtuella datorer med en formel som bas. Det finns två sätt att skapa formler: 

* Från en bas - Använd när du vill definiera alla egenskaper av formeln.
* Från en befintlig lab VM - användning när du vill skapa en formel som baseras på inställningarna för en befintlig virtuell dator.

Mer information om att lägga till användare och behörigheter finns i [Lägg till ägare och användare i Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Skapa en formel från en grundläggande
Följande steg vägleder dig genom processen att skapa en formel från en anpassad avbildning, Marketplace-avbildning eller en annan formel.

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.

3. Listan över labbar, Välj önskade labbet.  

4. På sidan för den testmiljön väljer **formler (återanvändbara baser)**.
   
    ![Formeln menyn](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. På den **formler** väljer **+ Lägg till**.
   
    ![Lägga till en formel](./media/devtest-lab-create-formulas/add-formula.png)

6. På den **väljer bas** väljer base (anpassad avbildning, Marketplace-avbildning eller formel) där du vill skapa formeln.
   
    ![Grundläggande lista](./media/devtest-lab-create-formulas/base-list.png)

7. På den **grundinställningar** fliken den **skapa formeln** anger du följande värden:
   
    * **Formelnamnet** -ange ett namn för din formel. Det här värdet visas i listan över grundläggande avbildningar när du skapar en virtuell dator. Kontrollera namnet som du skriver den, och om det är inte giltigt, ett meddelande som anger kraven för ett giltigt namn.
    * **Användarnamn** -ange ett användarnamn som har beviljats administratörsbehörighet.
    * **Lösenord** – ange - eller välj i listrutan - ett-värde som är associerad med den hemlighet (lösenord) som du vill använda för den angivna användaren. Läs om att spara hemligheter i key vault och använder dem när du skapar labbresurser i [Store hemligheter i Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).
    * **VM-storlek** – Välj **ändra storlek** ändrar storleken på den virtuella datorn. 
    * **Artefakter** – Välj **lägga till eller ta bort artefakter** sidan där du kan välja och konfigurera artefakter som du vill lägga till i basavbildningen. Läs mer om artefakter [skapa anpassade artefakter för din virtuella dator i Azure DevTest Labs](devtest-lab-artifact-author.md).
8. Växla till den **avancerade inställningar** fliken och ange följande värden:
    - **Virtuellt nätverk** – om du vill ändra det virtuella nätverket genom att välja **ändra Vnet**. 
    - **Undernät** – om du vill ändra undernätet, Välj **ändra undernätet**. 
    - **IP-adresskonfigurationen** – ange om du vill att de offentliga, privata eller delade IP-adresserna. Mer information om delade IP-adresser finns i [Förstå delade IP-adresser i Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Utgångsdatum och utgångstid** – ange datumet och tiden för den virtuella datorn så att den virtuella datorn tas bort automatiskt. 
    - **Gör till virtuell dator** – att göra en dator ”tillgängliga” innebär att den inte tilldelas ägarskap vid tidpunkten för skapandet. I stället kan labbanvändare bli ägare (”anspråk”) datorn i den testmiljön sidan.     
    - **Antalet tillgängliga instanser** – ange hur många tillgängliga instanser som du vill skapa. 
8. Välj **skicka** att skapa formeln.

9. När formeln har skapats visas den i listan på den **formler** sidan.

### <a name="create-a-formula-from-a-vm"></a>Skapa en formel från en virtuell dator
Följande steg vägleder dig genom processen att skapa en formel som baseras på en befintlig virtuell dator. 

> [!NOTE]
> Om du vill skapa en formel från en virtuell dator, måste den virtuella datorn har skapats efter den 30 mars 2016. 
> 
> 

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. Listan över labbar, Välj önskade labbet.  
4. På testmiljön **översikt** väljer du den virtuella datorn från vilken du vill skapa formeln.
   
    ![Labs virtuella datorer](./media/devtest-lab-create-formulas/my-vms.png)
5. På sidan för den Virtuella datorns väljer **skapa formeln (återanvändbara base)**.
   
    ![Skapa formeln](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. På den **skapa formeln** anger en **namn** och **beskrivning** för din nya formel.
   
    ![Skapa formeln sida](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Välj **OK** att skapa formeln.

## <a name="modify-a-formula"></a>Ändra en formel
Följ dessa steg om du vill ändra en formel:

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. Listan över labbar, Välj önskade labbet.  
4. På sidan för den testmiljön väljer **formler (återanvändbara baser)**.
   
    ![Formeln menyn](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. På den **Lab formler** väljer formeln som du vill ändra.
6. På den **Aktualizovat vzorec** , göra önskade ändringar och välj **uppdatering**.

## <a name="delete-a-formula"></a>Ta bort en formel
Följ dessa steg för att ta bort en formel:

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. Listan över labbar, Välj önskade labbet.  
4. På labbet **inställningar** väljer **formler**.
   
    ![Formeln menyn](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. På den **Lab formler** väljer du de tre punkterna till höger om formeln som du vill ta bort.
   
    ![Formeln menyn](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. På snabbmenyn för formelns väljer **ta bort**.
   
    ![Formeln snabbmenyn](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Välj **Ja** i bekräftelsedialogrutan för borttagning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Nästa steg
När du har skapat en formel för användning när du skapar en virtuell dator, nästa steg är att [lägga till en virtuell dator i labbet](devtest-lab-add-vm.md).

