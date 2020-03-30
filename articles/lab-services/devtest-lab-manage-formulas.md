---
title: Hantera formler i Azure DevTest Labs för att skapa virtuella datorer | Microsoft-dokument
description: Den här artikeln illustrerar hur du skapar en formel från antingen en bas (anpassad avbildning, Marketplace-avbildning eller en annan formel) eller en befintlig virtuell dator.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 4f8c8d4ff0a8014fe0b9a6ae4aead35ec7df4bf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501983"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Hantera Formler för Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Den här artikeln illustrerar hur du skapar en formel från antingen en bas (anpassad avbildning, Marketplace-avbildning eller en annan formel) eller en befintlig virtuell dator. Den här artikeln guidar dig också genom att hantera befintliga formler.

## <a name="create-a-formula"></a>Skapa en formel
Alla med behörigheter för DevTest *Labs-användare* kan skapa virtuella datorer med hjälp av en formel som bas. Det finns två sätt att skapa formler: 

* Från en bas - Använd när du vill definiera formelns alla egenskaper.
* Från en befintlig virtuell labb-VM - Använd när du vill skapa en formel baserat på inställningarna för en befintlig virtuell dator.

Mer information om hur du lägger till användare och behörigheter finns [i Lägga till ägare och användare i Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Skapa en formel från en bas
Följande steg hjälper dig att skapa en formel från en anpassad avbildning, Marketplace-avbildning eller en annan formel.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.

3. Välj önskat labb i listan över labb.  

4. På labbets sida väljer du **Formler (återanvändbara baser)** på den vänstra menyn.
5. På sidan **Formler** väljer du **+ Lägg till**.
   
    ![Lägga till en formel](./media/devtest-lab-create-formulas/add-formula.png)

6. På sidan **Välj en bas** väljer du den bas (anpassad bild eller Marketplace-avbildning) som du vill skapa formeln från.
7. Ange följande värden på fliken **Grundläggande inställningar** på formelsidan **Skapa:**
   
    * **Formelnamn** - Ange ett namn på formeln. Det här värdet visas i listan över basavbildningar när du skapar en virtuell dator. Namnet valideras när du skriver det, och om det inte är giltigt anger ett meddelande kraven för ett giltigt namn.
    - Ange en valfri **beskrivning** av formeln. 
    * **Användarnamn** - Ange ett användarnamn som beviljas administratörsbehörighet.
    * **Lösenord** - Ange - eller välj från listrutan - ett värde som är associerat med den hemlighet (lösenord) som du vill använda för den angivna användaren. Mer information om hur du sparar hemligheter i ett nyckelvalv och använder dem när du skapar labbresurser finns [i Lagra hemligheter i Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).

        Välj **Använd en sparad hemlighet** om du vill använda en hemlighet från Azure Key Vault i stället för att använda ett lösenord. 
    * **Storlek på virtuella datorer** - Välj **Ändra storlek** om du vill ändra storleken på den virtuella datorn. 
    - **OS-disktyp** - välj den typ av disk du vill använda (standard hdd, standard SSD eller Premium SSD).
    * **Artefakter** - Välj **Sidan Lägg till eller ta bort artefakter,** där du markerar och konfigurerar de artefakter som du vill lägga till i basavbildningen. Mer information om artefakter finns i [Skapa anpassade artefakter för den virtuella azure DevTest Labs-datorn](devtest-lab-artifact-author.md).

        ![Sidan Grundläggande inställningar](./media/devtest-lab-create-formulas/basic-settings.png)
8. Växla till fliken **Avancerade inställningar** och ange följande värden:
    - **Virtuellt nätverk** - Om du vill ändra det virtuella nätverket väljer du **Ändra Vnet**. 
    - **Undernät** - Om du vill ändra undernätet väljer du **Ändra undernät**. 
    - **IP-adresskonfiguration** - Ange om du vill ha de offentliga, privata eller delade IP-adresserna. Mer information om delade IP-adresser finns [i Förstå delade IP-adresser i Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Utgångsdatum och tid** - Du kan inte redigera det här fältet. 
    - **Gör den här datorn anspråksbar** - Att göra en maskin "anspråksbar" innebär att den inte kommer att tilldelas äganderätten vid tidpunkten för skapandet. Istället lab användare kommer att kunna ta ansvar ("anspråk") maskinen i labbets sida.  

        ![Sidan Grundläggande inställningar](./media/devtest-lab-create-formulas/advanced-settings.png)
8. Välj **Skicka** om du vill skapa formeln.

9. När formeln har skapats visas den i listan på sidan **Formler.**

### <a name="create-a-formula-from-a-vm"></a>Skapa en formel från en virtuell dator
Följande steg guidar dig genom processen att skapa en formel baserad på en befintlig virtuell dator. 

> [!NOTE]
> Om du vill skapa en formel från en virtuell dator måste den virtuella datorn ha skapats efter den 30 mars 2016. 
> 
> 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj önskat labb i listan över labb.  
4. På labbets **översiktssida** väljer du den virtuella dator som du vill skapa formeln från.
   
    ![Virtuella datorer i labb](./media/devtest-lab-create-formulas/my-vms.png)
5. På den virtuella datorns sida väljer du **Skapa formel (återanvändbar bas)**.
   
    ![Skapa formel](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. På sidan **Skapa formel** anger du ett **namn** och **en beskrivning** för den nya formeln.
   
    ![Sidan Skapa formel](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Välj **OK** om du vill skapa formeln.

## <a name="modify-a-formula"></a>Ändra en formel
Så här ändrar du en formel:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj önskat labb i listan över labb.  
4. På labbets sida väljer du **Formler (återanvändbara baser)**.
   
    ![Formel-menyn](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. På sidan **Lab-formler** väljer du den formel som du vill ändra.
6. På **sidan Uppdatera formel** gör du önskade ändringar och väljer **Uppdatera**.

## <a name="delete-a-formula"></a>Ta bort en formel
Så här tar du bort en formel:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj önskat labb i listan över labb.  
4. Välj **Formler**på sidan **Labbinställningar** .
   
    ![Formel-menyn](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. På sidan **Lab-formler** väljer du ellipsen till höger om den formel som du vill ta bort.
   
    ![Formel-menyn](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Välj **Ta bort**på formelns snabbmeny .
   
    ![Snabbmeny för formel](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Välj **Ja** i dialogrutan för borttagningsbekräftelse.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Anpassade bilder eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Nästa steg
När du har skapat en formel som ska användas när du skapar en virtuell dator är nästa steg att lägga till [en virtuell dator i labbet](devtest-lab-add-vm.md).

