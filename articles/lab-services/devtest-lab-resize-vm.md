---
title: Ändra storlek på en virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du ändrar storlek på en virtuell dator i Azure DevTest Labs
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
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: a0bc618a9c0a02aae884d8be359df6bdbf4c0d2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868102"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Ändra storlek på en virtuell dator i ett labb i Azure DevTest Labs
En av de viktigaste funktionerna i Azure-datorer är att du kan ändra storlek på en virtuell dator (VM) som utifrån dina behov för processor, nätverk eller diskprestanda. Azure DevTest Labs stöder den här funktionen för virtuella datorer i ett labb nu. Funktionen för storleksändring följer labb-princip för tillåtna storlekar för Virtuella datorer i labbet. Det vill säga kan du ändra storleken på en virtuell dator för att endast tillåtna storlekar i laboratoriet. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Steg för att ändra storlek på en virtuell dator i ett labb 
Om du vill ändra storlek på en virtuell dator i ett labb i Azure DevTest Labs, gör du följande: 

> [!NOTE]
> Om du är ansluten till den virtuella datorn via en fjärrskrivbordssession (RDP), spara ditt arbete och koppla från den virtuella datorn innan du ändrar storlek på den.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. I listan över labbar Välj labb som innehåller den virtuella datorn som du vill ändra storlek.  
4. I den vänstra panelen, väljer **Mina virtuella datorer**. 
5. Välj en virtuell dator från listan över virtuella datorer.
6. Välj **stoppa** i verktygsfältet om Virtuellt datorn körs. Kontrollera status för åtgärden i den **meddelanden** fönster. Vänta tills den virtuella datorn har stoppats och stänger sedan den **meddelanden** fönster. 

    ![Stoppa den virtuella datorn](media/devtest-lab-resize-vm/stop-vm.png)
1. På sidan virtuell dator för den virtuella datorn, Välj **storlek** under **inställningar** på den vänstra menyn.

    ![Storlek-menyn](media/devtest-lab-resize-vm/size-menu.png)
1. I den **väljer du en storlek** , bläddra och välj en storlek för den virtuella datorn, och klicka på **Välj**.     
1. Kontrollera status för storleksändringen i den **meddelanden** fönster.

    ![Ändra storlek på status](media/devtest-lab-resize-vm/resize-status.png)
10. När storleksändringen utförts åtgärden misslyckas, Stäng den **meddelanden** fönster. 
11. Välj **översikt** i den vänstra menyn och välj **starta om** i verktygsfältet för att starta om den virtuella datorn. 

## <a name="next-steps"></a>Nästa steg
Detaljerad information om funktionen för storleksändring som stöds av Azure-datorer finns i [ändra storlek på virtuella datorer](https://azure.microsoft.com/blog/resize-virtual-machines/).


