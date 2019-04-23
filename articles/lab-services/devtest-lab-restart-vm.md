---
title: Starta om en virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du startar om en virtuell dator i Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 34c08a79abf6acb5ae8582ecd0743a890d850fc8
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149885"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Starta om en virtuell dator i ett labb i Azure DevTest Labs
Du kan snabbt och starta om enkelt en virtuell dator i DevTest Labs genom att följa stegen i den här artikeln. Tänk på följande innan du startar om en virtuell dator:

- Den virtuella datorn måste köras för funktionen omstart är aktiverat.
- Om en användare är ansluten till en aktiv virtuell dator när de utför en omstart, måste de återansluta till den virtuella datorn när den börjar säkerhetskopiera.
- Om en artefakt som används när du startar om den virtuella datorn, får du en varning artefakten inte kanske tillämpas.

    ![Varning när du startar om när artefakter](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Om den virtuella datorn har stoppats samtidigt som du använder en artefakt kan använda du funktionen omstart av virtuell dator som ett potentiella sätt att lösa problemet.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Steg för att starta om en virtuell dator i ett labb i Azure DevTest Labs
1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. I listan över labbar Välj labb som innehåller den virtuella datorn som du vill starta om.
1. I den vänstra panelen, väljer **Mina virtuella datorer**.
1. Välj en aktiv virtuell dator från listan över virtuella datorer.
1. Högst upp i fönstret för hantering av virtuell dator, Välj **starta om**.

    ![Starta om VM-knappen](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Övervaka status för omstart genom att välja den **meddelanden** längst upp höger i fönstret.

    ![Visa status för VM-omstart](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Du kan också starta om en aktiv virtuell dator genom att välja dess ellips (...) i listan över **Mina virtuella datorer**.

![Starta om virtuell dator via ellipserna](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Nästa steg
* När den startas om, du kan återansluta till den virtuella datorn genom att välja **Connect** på i hanteringsfönstret av dess.
* Utforska den [DevTest Labs Azure Resource Manager-mall snabbstartsgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
