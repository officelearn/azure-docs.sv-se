---
title: Starta om en virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: "Lär dig hur du startar om en virtuell dator i Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: v-craic
ms.openlocfilehash: ffa669e2d07a93e9e5071ac8aab81ec14ea24eab
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Starta om en virtuell dator i ett labb i Azure DevTest Labs
Du kan snabbt och starta om enkelt en virtuell dator i DevTest Labs genom att följa stegen i den här artikeln. Tänk på följande innan du startar om en virtuell dator:

- Den virtuella datorn måste köras för att funktionen omstart ska aktiveras.
- Om en användare är ansluten till en aktiv virtuell dator när de utför en omstart, måste de ansluta till den virtuella datorn när den börjar säkerhetskopiera.
- Om en artefakt som används när du startar om den virtuella datorn, visas ett varningsmeddelande som artefakten inte kan användas. 

    ![Varning när du startar om när du använder artefakter](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Om den virtuella datorn har stoppats samtidigt som du använder en artefakt kan använda du funktionen omstart VM potentiella för att lösa problemet.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Steg för att starta om en virtuell dator i ett labb i Azure DevTest Labs
1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
1. Välj i listan över labs labb som innehåller den virtuella datorn som du vill starta om.  
1. I den vänstra rutan, Välj **Mina virtuella datorer**. 
1. Välj en aktiv virtuell dator i listan över virtuella datorer.
1. Längst upp i fönstret VM management, Välj **starta om**.  

    ![Starta om VM-knappen](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Övervaka status för omstarten genom att välja den **meddelanden** längst upp till höger i fönstret.

    ![Visa status för VM-omstart](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Du kan också starta om en aktiv virtuell dator genom att välja dess ellips (...) i listan över **Mina virtuella datorer**.

![Starta om virtuella datorn via ellipserna](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Nästa steg
* När den startas om och du kan återansluta till den virtuella datorn genom att välja **Anslut** på den dess hanteringsfönstret.
* Utforska den [DevTest Labs Azure Resource Manager snabbstartsgalleriet mall](https://github.com/Azure/azure-devtestlab/tree/master/Samples)
