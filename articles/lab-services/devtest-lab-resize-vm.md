---
title: Ändra storlek på en virtuell dator i ett labb i Azure DevTest Labs
description: Lär dig hur du ändrar storleken på en virtuell dator (VM) i Azure DevTest Labs baserat på dina behov för CPU, nätverk eller diskprestanda.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bf7c425766a97aaa1d143133f04502a0aa3c36cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756185"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Ändra storlek på en virtuell dator i ett labb i Azure DevTest Labs
En av de viktiga funktionerna i virtuella Azure-datorer är att du kan ändra storleken på en virtuell dator (VM) baserat på dina behov av CPU- eller nätverksprestanda. Azure DevTest Labs stöder den här funktionen för virtuella datorer i ett labb nu. Funktionen Ändra storlek följer labbprincipen för tillåtna VM-storlekar i labbet. Det vill säga, du kan ändra storleken på en virtuell dator till endast tillåtna storlekar i labbet. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Åtgärder för att ändra storlek på en virtuell dator i ett labb 
Så här ändrar du storlek på en virtuell dator i ett labb i Azure DevTest Labs: 

> [!NOTE]
> Om du är ansluten till den virtuella datorn via en fjärrskrivbordssession (RDP), spara ditt arbete och koppla från den virtuella datorn innan du ändrar storlek på den.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj labbet som innehåller den virtuella datorn som du vill ändra storlek på i listan över labb.  
4. Välj Mina virtuella **datorer på**den vänstra panelen . 
5. Välj en virtuell dator i listan över virtuella datorer.
6. Välj **Stopp** i verktygsfältet om den virtuella datorn körs. Kontrollera status för åtgärden i fönstret **Meddelanden.** Vänta tills den virtuella datorn har stoppats och stäng sedan fönstret **Meddelanden.** 

    ![Stoppa den virtuella datorn](media/devtest-lab-resize-vm/stop-vm.png)
1. På sidan Virtuell dator för den virtuella datorn väljer du **Storlek** under **INSTÄLLNINGAR** i den vänstra menyn.

    ![Menyn Storlek](media/devtest-lab-resize-vm/size-menu.png)
1. Bläddra i och välj en storlek för den virtuella datorn i fönstret **Välj storlek** och klicka på **Välj**.     
1. Kontrollera status för åtgärden ändra storlek i fönstret **Meddelanden.**

    ![Ändra storlek på status](media/devtest-lab-resize-vm/resize-status.png)
10. När åtgärden ändra storlek lyckas stänger du fönstret **Meddelanden.** 
11. Välj **Översikt** på den vänstra menyn och välj **Starta om** i verktygsfältet för att starta om den virtuella datorn. 

## <a name="next-steps"></a>Nästa steg
Detaljerad information om funktionen ändra storlek som stöds av virtuella Azure-datorer finns i [Ändra storlek på virtuella datorer](https://azure.microsoft.com/blog/resize-virtual-machines/).


