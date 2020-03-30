---
title: Ta bort ett labb eller en virtuell dator i ett labb i Azure DevTest Labs
description: Den här artikeln visar hur du tar bort ett labb eller tar bort en virtuell dator i ett labb med Azure-portalen (Azure DevTest Labs).
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 28fb464714f464a4c0a8f5eaf304dcdd5d603c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270815"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Ta bort ett labb eller en virtuell dator i ett labb i Azure DevTest Labs
Den här artikeln visar hur du tar bort ett labb eller en virtuell dator i ett labb.

## <a name="delete-a-lab"></a>Ta bort ett labb
När du tar bort en DevTest Labs-instans från en resursgrupp utför devtest labs-tjänsten följande åtgärder: 

- Alla resurser som skapades automatiskt när labbet skapades tas bort automatiskt. Själva resursgruppen tas inte bort. Om du manuellt har skapat resurser som den här resursgruppen har skapat tas inte dem bort. 
- Alla virtuella datorer i labb- och resursgrupper som är associerade med dessa virtuella datorer tas bort automatiskt. När du skapar en virtuell dator i ett labb skapar tjänsten resurser (disk, nätverksgränssnitt, offentlig IP-adress osv.) för den virtuella datorn i en separat resursgrupp. Men om du manuellt skapar ytterligare resurser i dessa resursgrupper tar devtest labs-tjänsten inte bort dessa resurser och resursgruppen. 

Så här tar du bort ett labb: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser** från menyn till vänster, välj **DevTest Labs** för typen av tjänst och välj labbet.

    ![Välj ditt labb](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Klicka på **Ta bort** i verktygsfältet på sidan **DevTest Lab.** 

    ![Knappen Ta bort](media/devtest-lab-delete-lab-vm/delete-button.png)
4. På sidan **Bekräftelse** anger du **namnet** på labbet och väljer **Ta bort**. 

    ![Bekräfta](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Om du vill se åtgärdens status väljer du **ikonen Meddelanden** (Bell). 

    ![Meddelanden](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Ta bort en virtuell dator i ett labb
Om jag tar bort en virtuell dator i ett labb tas några av de resurser (inte alla) som skapades vid tidpunkten för labbet bort. Följande resurser tas inte bort: 

-   Nyckelvalv i huvudresursgruppen
-   Tillgänglighetsuppsättning, belastningsutjämnare, offentlig IP-adress i vm-resursgruppen. Dessa resurser delas av flera virtuella datorer i en resursgrupp. 

Virtuell dator, nätverksgränssnitt och disk som är associerad med den virtuella datorn tas bort. 

Så här tar du bort en virtuell dator i ett labb: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser** från menyn till vänster, välj **DevTest Labs** för typen av tjänst och välj labbet.

    ![Välj ditt labb](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Välj **... (ellips)** för den virtuella datorn i listan över virtuella datorer och välj **Ta bort**. 

    ![Ta bort virtuell dator på menyn](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. Välj **Ok** **i** bekräftelsedialogrutan . 
5. Om du vill se åtgärdens status väljer du **ikonen Meddelanden** (Bell). 

Om du vill ta bort en virtuell dator från **sidan Virtuell dator**väljer du Ta **bort** från verktygsfältet som visas i följande bild:

![Ta bort virtuell dator från vm-sidan](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Nästa steg
Om du vill skapa ett labb läser du följande artiklar: 

- [Skapa ett labb](devtest-lab-create-lab.md)
- [Lägga till en virtuell dator i labbet](devtest-lab-add-vm.md)