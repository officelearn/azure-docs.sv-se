---
title: Ta bort en labb eller virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln visar hur du tar bort en labb eller virtuell dator i ett labb.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: 99caf04698226de8daa9cfb8f60662e5cb0f8b49
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449829"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Ta bort en labb eller virtuell dator i ett labb i Azure DevTest Labs
Den här artikeln visar hur du tar bort en labb eller virtuell dator i ett labb.

## <a name="delete-a-lab"></a>Ta bort ett labb
När du tar bort en DevTest Labs-instans från en resursgrupp utför DevTest Labs-tjänsten följande åtgärder: 

- Alla resurser som skapades automatiskt när den skapas i labb tas bort automatiskt. Själva resursgruppen tas inte bort. Om du manuellt har skapat alla resurser som den här resursgruppen bort inte tjänsten dem. 
- Alla virtuella datorer i labbet och -resursgrupper som kopplade till dessa virtuella datorer tas bort automatiskt. Tjänsten skapar resurser (disk, nätverksgränssnitt, offentliga IP-adressen, osv.) för den virtuella datorn i en separat resursgrupp när du skapar en virtuell dator i ett labb. Men om du manuellt skapar eventuella ytterligare resurser i dessa resursgrupper, tas DevTest Labs-tjänsten inte bort resurserna och resursgruppen. 

Om du vill ta bort ett labb, gör du följande åtgärder: 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla** menyn till vänster och välj **DevTest Labs** för typ av tjänst och välj labbet.

    ![Välj ditt labb](media\devtest-lab-delete-lab-vm\select-lab.png)
3. På den **labb** klickar du på **ta bort** i verktygsfältet. 

    ![Ta bort knapp](media\devtest-lab-delete-lab-vm\delete-button.png)
4. På den **bekräftelse** anger du den **namn** om ditt labb och markera **ta bort**. 

    ![Bekräfta](media\devtest-lab-delete-lab-vm\confirm-delete.png)
5. Om du vill se status för åtgärden **meddelanden** ikonen (klockan). 

    ![Meddelanden](media\devtest-lab-delete-lab-vm\delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Ta bort en virtuell dator i ett labb
Om jag tar bort en virtuell dator i ett labb kan tas vissa av de (inte alla) resurserna som skapades när den skapas i labb bort. Följande resurser tas inte bort: 

-   Nyckelvalv i den huvudsakliga resursgruppen
-   Tillgänglighet ange, belastningsutjämnare, offentlig IP-adress i resursgrupp för virtuell dator. Dessa resurser som delas av flera virtuella datorer i en resursgrupp. 

Virtuell dator, nätverksgränssnitt och disk som är associerade med den virtuella datorn tas bort. 

Om du vill ta bort en virtuell dator i ett labb, gör du följande åtgärder: 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla** menyn till vänster och välj **DevTest Labs** för typ av tjänst och välj labbet.

    ![Välj ditt labb](media\devtest-lab-delete-lab-vm\select-lab.png)
3. Välj **... (tre punkter)**  för den virtuella datorn i listan över virtuella datorer och välj **ta bort**. 

    ![Ta bort virtuell dator i menyn](media\devtest-lab-delete-lab-vm\delete-vm-menu-in-list.png)
4. På den **bekräftelse** dialogrutan **Ok**. 
5. Om du vill se status för åtgärden **meddelanden** ikonen (klockan). 

Att ta bort en virtuell dator från den **VM sidan**väljer **ta bort** från verktygsfältet, som visas i följande bild:

![Ta bort virtuell dator från VM-sida](media\devtest-lab-delete-lab-vm\delete-from-vm-page.png) 


## <a name="next-steps"></a>Nästa steg
Om du vill skapa ett labb finns i följande artiklar: 

- [Skapa ett labb](devtest-lab-create-lab.md)
- [Lägg till en virtuell dator i labbet](devtest-lab-add-vm.md)