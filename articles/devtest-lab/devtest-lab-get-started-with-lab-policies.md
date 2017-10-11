---
title: "Hantera principer för grundläggande labbet i Azure DevTest Labs | Microsoft Docs"
description: "Lär dig hur du ställer in några av de grundläggande principerna (inställningar) för ett labb i DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: tarcher
ms.openlocfilehash: ed35d081b191ec41ed9e5970515057a4715c0d59
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Hantera grundläggande principer för ett labb i Azure DevTest Labs

Azure DevTest Labs kan du styra kostnader och minimera skräp i din labs genom att hantera principer (inställningar) för varje labbet. I den här artikeln får komma du igång med principer genom att lära dig hur du ställer in två av de viktigaste principerna - begränsning av antalet virtuella datorer (VM) som kan skapas eller ägs av en enskild användare och konfigurera automatisk avstängning. Så här visar hur du ställer in varje lab princip finns i artikeln [Definiera principer för labbet i Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Åtkomst till ett labb principer i Azure DevTest Labs
Följande steg hur du konfigurerar principer för ett labb i Azure DevTest Labs:

Om du vill visa, och ändra principer för ett labb, gör du följande:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Fler tjänster** och välj sedan **DevTest Labs** från listan.

1. Lista över labs, Välj önskade labbet.   

1. Välj **konfiguration och principer**.

    ![Principen inställningsbladet](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Den **konfiguration och principer** bladet innehåller en meny med inställningar som du kan ange. Den här artikeln beskriver bara till inställningarna för **virtuella datorer per användare** och **automatisk avstängning**. Läs om de återstående inställningarna i [hantera alla principer för ett labb i Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Ange virtuella datorer per användare
Principen för **virtuella datorer per användare** kan du ange det maximala antalet virtuella datorer som kan skapas av en enskild användare. Om en användare försöker skapa eller begära en virtuell dator när antalet användare som har uppfyllts, ett felmeddelande som anger att den virtuella datorn inte kan skapas/anspråk. 

1. På testmiljön **konfiguration och principer** väljer du **virtuella datorer per användare**.
   
    ![Virtuella datorer per användare](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Välj **Ja** begränsar antalet virtuella datorer per användare. Om du inte vill begränsa antalet virtuella datorer per användare väljer **nr**. Om du väljer **Ja**, ange ett numeriskt värde som anger maximalt antal virtuella datorer som kan skapas eller ägs av en användare. 

1. Välj **Ja** begränsar antalet virtuella datorer som kan använda SSD (SSD disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD, väljer **nr**. Om du väljer **Ja**, ange ett värde som anger maximalt antal virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-auto-shutdown"></a>Ange automatisk avstängning
Principen för automatisk avstängning hjälper till att minimera skräp lab genom att du kan ange hur lång tid som den här övningen VMs stängs.

1. På testmiljön **konfiguration och principer** bladet väljer **automatisk avstängning**.
   
    ![Automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Välj **på** att aktivera den här principen och **av** du inaktivera den.

1. Om du aktiverar den här principen kan du ange tid (och tidszon) för att stänga av alla virtuella datorer i det aktuella labbet.

1. Ange **Ja** eller **nr** för alternativet att skicka en avisering 15 minuter innan den angivna automatisk avstängning tid. Om du anger **Ja**, ange en webhook URL-slutpunkt för att ta emot meddelandet. Läs mer om webhooks [skapa en webhook eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Välj **Spara**.

    Som standard när du har aktiverat, den här principen gäller för alla virtuella datorer i det aktuella labbet. Om du vill ta bort den här inställningen från en specifik VM, öppna bladet för den virtuella datorn och ändra dess **automatisk avstängning** inställning 

## <a name="set-auto-start"></a>Ange automatisk start
Principen för automatisk start kan du ange när de virtuella datorerna i det aktuella labbet ska startas.  

1. På testmiljön **konfiguration och principer** bladet väljer **Autostart**.
   
    ![Automatisk start](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Välj **på** att aktivera den här principen och **av** du inaktivera den.

3. Om du aktiverar den här principen kan du ange den schemalagda starttiden, tidszon och veckodagar som gäller tid. 

4. Välj **Spara**.

    När du har aktiverat, tillämpas inte principen automatiskt till alla virtuella datorer i det aktuella labbet. Öppna bladet för den virtuella datorn för att använda den här inställningen för en specifik VM, och ändra dess **Autostart** inställning 

## <a name="next-steps"></a>Nästa steg

- [Definiera principer för labbet i Azure DevTest Labs](devtest-lab-set-lab-policy.md) – Lär dig hur du ändrar andra principer för labbet 
