---
title: "Hantera formler i Azure DevTest Labs för att skapa virtuella datorer | Microsoft Docs"
description: "Lär dig att uppdatera och ta bort Azure DevTest Labs formler"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfdab5def50158f9b764bbb1e50c2624cc6d5fb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-devtest-labs-formulas"></a>Hantera Azure DevTest Labs formler

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Den här artikeln beskrivs hur du skapar en formel från en bas (anpassad avbildning, Marketplace-avbildning eller en annan metod) eller en befintlig virtuell dator. Den här artikeln hjälper dig att hantera befintliga formler också.

## <a name="create-a-formula"></a>Skapa en formel
Alla med DevTest Labs *användare* behörigheter är att skapa virtuella datorer med en formel som bas. Det finns två sätt att skapa formler: 

* Använd när du vill definiera alla av formeln egenskaper från en bas.
* Använd när du vill skapa en formel baserat på inställningarna för en befintlig virtuell dator från en befintlig lab VM.

Mer information om att lägga till användare och behörigheter finns [lägga till ägare och användare i Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Skapa en formel från en bas
Följande steg leder dig genom processen att skapa en formel från en anpassad avbildning, Marketplace-avbildning eller en annan formel.

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.

3. Lista över labs, Välj önskade labbet.  

4. På den testmiljön bladet välj **formler (återanvändbara baser)**.
   
    ![Formeln menyn](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. På den **formler** bladet väljer **+ Lägg till**.
   
    ![Lägga till en formel](./media/devtest-lab-create-formulas/add-formula.png)

6. På den **väljer du en bas** bladet välj base (anpassad avbildning, Marketplace-avbildning eller metod) från vilken du vill skapa formeln.
   
    ![Grundläggande lista](./media/devtest-lab-create-formulas/base-list.png)

7. På den **skapa formeln** bladet anger du följande värden:
   
    * **Formelnamnet** -ange ett namn för din formel. Det här värdet visas i listan över grundläggande bilder när du skapar en virtuell dator. Verifiera namnet på som du skriver du det och om det är inte giltig, ett meddelande som anger kraven för ett giltigt namn.
    * **Beskrivning** -ange en beskrivning för din formel. Det här värdet är tillgängligt formelns snabbmenyn när du skapar en virtuell dator.
    * **Användarnamnet** -ange ett användarnamn som har beviljats administratörsbehörighet.
    * **Lösenordet** – Skriv - eller välj i listrutan - ett värde som är associerade med den hemlighet (lösenord) som du vill använda för den angivna användaren. Mer information om hemligheterna finns [Azure DevTest Labs: hemliga datorarkivet](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/).
    * **Typ av virtuell dator disk** – ange antingen Hårddisk (hårddiskenheten) eller SSD (SSD) som anger vilken lagringstyp som disk tillåts för de virtuella datorerna etableras med den här grundläggande bild.
    * ** Virtuella storlek ** – Välj något av de fördefinierade objekt som anger processorkärnor, ram-storlek och storleken på hårddisken på den virtuella datorn för att skapa. 
    * **Artefakter** – Välj för att öppna den **lägga till artefakter** bladet, där du kan välja och konfigurera artefakter som du vill lägga till basavbildningen. Läs mer om artefakter [hantera VM artefakter i Azure DevTest Labs](./devtest-lab-add-vm-with-artifacts.md).
    * **Avancerade inställningar** – Välj för att öppna den **Avancerat** bladet där du kan konfigurera följande inställningar:
        * **Virtuellt nätverk** -ange det önskade virtuella nätverket.
        * **Undernät** – ange det önskade undernätet.    
        * **IP-adresskonfiguration** -ange om du vill Public, Private eller delade IP-adresser. Mer information om delade IP-adresser finns [Förstå delade IP-adresser i Azure DevTest Labs](./devtest-lab-shared-ip.md).
        * **Se den här datorn claimable** -gör en dator ”claimable” innebär att den inte tilldelas ägarskap vid tidpunkten för skapandet. I stället kommer lab-användare att kunna bli ägare (”anspråk”) datorn i den testmiljön bladet.     
    * **Bild** -det här fältet visar namnet på basavbildningen du valde på föregående blad. 
     
       ![Skapa formeln](./media/devtest-lab-create-formulas/create-formula.png)

8. Välj **skapa** att skapa formeln.

9. När formeln har skapats kan den visas i listan på den **formler** bladet.

### <a name="create-a-formula-from-a-vm"></a>Skapa en formel från en virtuell dator
Följande steg leder dig genom processen att skapa en formel som baseras på en befintlig virtuell dator. 

> [!NOTE]
> Om du vill skapa en formel från en virtuell dator måste den virtuella datorn har skapats efter den 30 mars 2016. 
> 
> 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
3. Lista över labs, Välj önskade labbet.  
4. På testmiljön **översikt** bladet Välj den virtuella datorn från vilken du vill skapa formeln.
   
    ![Labs virtuella datorer](./media/devtest-lab-create-formulas/my-vms.png)
5. På bladet för den virtuella datorn, väljer **skapa formeln (återanvändbara base)**.
   
    ![Skapa formeln](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. På den **skapa formeln** bladet anger du en **namn** och **beskrivning** för din nya formel.
   
    ![Skapa formeln bladet](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Välj **OK** att skapa formeln.

## <a name="modify-a-formula"></a>Ändra en formel
Följ dessa steg om du vill ändra en formel:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
3. Lista över labs, Välj önskade labbet.  
4. På den testmiljön bladet välj **formler (återanvändbara baser)**.
   
    ![Formeln menyn](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. På den **Lab formler** bladet Välj formeln som du vill ändra.
6. På den **uppdatera formel** bladet gör önskade ändringar och välj **uppdatering**.

## <a name="delete-a-formula"></a>Ta bort en formel
Följ dessa steg om du vill ta bort en formel:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
3. Lista över labs, Välj önskade labbet.  
4. Om labbet **inställningar** bladet väljer **formler**.
   
    ![Formeln menyn](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. På den **Lab formler** bladet Välj tre punkter till höger om formeln som du vill ta bort.
   
    ![Formeln menyn](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Markera på formelns snabbmenyn **ta bort**.
   
    ![Formeln snabbmenyn](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Välj **Ja** till dialogrutan för borttagning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Nästa steg
När du har skapat en formel för användning när du skapar en virtuell dator, nästa steg är att [lägga till en virtuell dator i labbet](devtest-lab-add-vm-with-artifacts.md).

