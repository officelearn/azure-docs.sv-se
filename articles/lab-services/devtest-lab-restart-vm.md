---
title: Starta om en virtuell dator i ett labb i Azure DevTest Labs | Microsoft-dokument
description: Den här artikeln innehåller steg för att snabbt och enkelt starta om virtuella datorer (VM) i Azure DevTest Labs.
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
ms.openlocfilehash: 52d3b92909483a99eb82c86b727261bbeb5f8d46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760002"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Starta om en virtuell dator i ett labb i Azure DevTest Labs
Du kan snabbt och enkelt starta om en virtuell dator i DevTest Labs genom att följa stegen i den här artikeln. Tänk på följande innan du startar om en virtuell dator:

- Den virtuella datorn måste köras för att omstartsfunktionen ska aktiveras.
- Om en användare är ansluten till en virtuell dator som körs när de utför en omstart måste de återansluta till den virtuella datorn när den startar säkerhetskopieringen.
- Om en artefakt används när du startar om den virtuella datorn får du en varning om att artefakten kanske inte tillämpas.

    ![Varning vid omstart vid användning av artefakter](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Om den virtuella datorn har kört fast när du använder en artefakt kan du använda funktionen starta om virtuell dator som ett potentiellt sätt att lösa problemet.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Steg för att starta om en virtuell dator i ett labb i Azure DevTest Labs
1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
1. Välj labbet som innehåller den virtuella datorn som du vill starta om i listan över labb.
1. Välj Mina virtuella **datorer på**den vänstra panelen .
1. Välj en virtuell dator som körs i listan över virtuella datorer.
1. Högst upp i hanteringsfönstret för virtuella datorer väljer du **Starta om**.

    ![Knappen Starta om virtuell dator](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Övervaka omstartens status genom att välja ikonen **Meddelanden** längst upp till höger i fönstret.

    ![Visa status för omstarten av den virtuella datorn](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Du kan också starta om en virtuell dator som körs genom att välja dess ellips (...) i listan över **Mina virtuella datorer**.

![Starta om den virtuella datorn via ellipser](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Nästa steg
* När den har startats om kan du återansluta till den virtuella datorn genom att välja **Anslut** i hanteringsfönstret.
* Utforska [snabbstartsgalleriet DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
