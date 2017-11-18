---
title: "Hantera principer för labbet i Azure DevTest Labs | Microsoft Docs"
description: "Lär dig hur du definierar principer för labbet som VM-storlekar och maximala virtuella datorer per användare och avstängning automation."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f42baa1ac37f425f915b6949e018f6e7397d3ae
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Hantera alla principer för ett labb i Azure DevTest Labs

Azure DevTest Labs kan du styra kostnader och minimera skräp i din labs genom att hantera principer (inställningar) för varje labbet. Den här artikeln förklarar i detalj steg för steg hur du ställer in varje princip.  

## <a name="set-allowed-virtual-machine-sizes"></a>Ange tillåtna storlekar för virtuella datorer
Principen för att ange tillåtna VM-storlekar hjälper till att minimera skräp lab genom att du kan ange vilka VM-storlekar som tillåts i labbet. Om den här principen aktiveras användas endast VM-storlekar från den här listan för att skapa virtuella datorer.

1. I den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), Välj ett labb och välj sedan **konfiguration och principer**.

    ![Åtkomst till testmiljön konfiguration och principer](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. På testmiljön **konfiguration och principer** väljer **tillåtna storlekar för virtuella datorer**.
   
    ![Tillåtna virtuella datorer storlekar](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Välj **på** att aktivera den här principen och **av** du inaktivera den.

1. Om du aktiverar den här principen väljer du en eller flera VM-storlekar som kan skapas i labbet.

1. Välj **Spara**.

## <a name="set-virtual-machines-per-user"></a>Ange virtuella datorer per användare
Principen för **virtuella datorer per användare** kan du ange det maximala antalet virtuella datorer som kan skapas av en enskild användare. Om en användare försöker skapa eller begära en virtuell dator när antalet användare som har uppfyllts, ett felmeddelande som anger att den virtuella datorn inte kan skapas/anspråk. 

1. På testmiljön **konfiguration och principer** väljer **virtuella datorer per användare**.
   
    ![Virtuella datorer per användare](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Välj **Ja** begränsar antalet virtuella datorer per användare. Om du inte vill begränsa antalet virtuella datorer per användare väljer **nr**. Om du väljer **Ja**, ange ett numeriskt värde som anger maximalt antal virtuella datorer som kan skapas eller ägs av en användare. 

1. Välj **Ja** begränsar antalet virtuella datorer som kan använda SSD (SSD disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD, väljer **nr**. Om du väljer **Ja**, ange ett värde som anger maximalt antal virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-virtual-machines-per-lab"></a>Ange virtuella datorer per labb
Principen för **virtuella datorer per labb** kan du ange det maximala antalet virtuella datorer som kan skapas för det aktuella labbet. Om en användare försöker skapa en virtuell dator när lab gränsen har uppfyllts, ett felmeddelande som anger att det inte går att skapa den virtuella datorn. 

1. På testmiljön **konfiguration och principer** väljer **virtuella datorer per labb**.
   
    ![Virtuella datorer per labb](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Välj **Ja** begränsar antalet virtuella datorer per labb. Om du inte vill begränsa antalet virtuella datorer per labb väljer **nr**. Om du väljer **Ja**, ange ett numeriskt värde som anger maximalt antal virtuella datorer som kan skapas eller ägs av en användare. 

1. Välj **Ja** begränsar antalet virtuella datorer som kan använda SSD (SSD disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD, väljer **nr**. Om du väljer **Ja**, ange ett värde som anger maximalt antal virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-auto-shutdown"></a>Ange automatisk avstängning
Principen för automatisk avstängning bidrar till att minimera skräp lab genom att du kan ange hur lång tid som den här övningen VMs stängs.

1. På testmiljön **konfiguration och principer** väljer **automatisk avstängning**.
   
    ![Automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Välj **på** att aktivera den här principen och **av** du inaktivera den.

1. Om du aktiverar den här principen kan du ange tid (och tidszon) för att stänga av alla virtuella datorer i det aktuella labbet.

1. Ange **Ja** eller **nr** för alternativet att skicka en avisering 15 minuter innan den angivna automatisk avstängning tid. Om du väljer **Ja**, ange en webhook URL-slutpunkt eller en e-postadress som anger där du vill att meddelanden ska anslås eller skickas. Användaren får ett meddelande och ges möjlighet att skjuta avstängningen.

   Läs mer om webhooks [skapa en webhook eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Välj **Spara**.

Som standard när du har aktiverat, den här principen gäller för alla virtuella datorer i det aktuella labbet. Om du vill ta bort den här inställningen från en specifik VM, öppna hanteringsfönstret för den virtuella datorn och ändra dess **automatisk avstängning** inställningen.

## <a name="set-auto-start"></a>Ange automatisk start
Principen för automatisk start kan du ange när de virtuella datorerna i det aktuella labbet ska startas.  

1. På testmiljön **konfiguration och principer** väljer **Autostart**.
   
    ![Automatisk start](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Välj **på** att aktivera den här principen och **av** du inaktivera den.

3. Om du aktiverar den här principen kan du ange den schemalagda starttiden, tidszon och veckodagar som gäller tid. 

4. Välj **Spara**.

När du har aktiverat, tillämpas inte principen automatiskt till alla virtuella datorer i det aktuella labbet. För att använda den här inställningen för en specifik VM, öppna hanteringsfönstret för den virtuella datorn och ändra dess **Autostart** inställningen.

## <a name="set-expiration-date"></a>Ange förfallodatum
Du kan ange ett förfallodatum när du [skapa den virtuella datorn](devtest-lab-add-vm.md). I **avancerade inställningar**, väljer du kalenderikonen för att ange ett datum då den virtuella datorn automatiskt bort. Som standard den virtuella datorn upphör aldrig att gälla.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har definierat och tillämpas olika VM-principinställningarna för övningen, är här några saker att försöka:

* [Förstå delade IP-adresser](devtest-lab-shared-ip.md) -förklarar hur delade IP-adresserna används i DevTest Labs för att minimera antalet offentliga IP-adresser som krävs för att ansluta till ditt labb virtuella datorer.
* [Konfigurera hantering av kostnaden](devtest-lab-configure-cost-management.md) -illustrerar hur du använder den **månatlig uppskattad Kostnadstrend** diagram  
  Om du vill visa den aktuella månaden är uppskattade kostnaden-till-date och planerade sista månad kostnaden.
* [Skapa den anpassade bilden](devtest-lab-create-template.md) – när du skapar en virtuell dator, anger du en bas som kan vara antingen en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln beskrivs hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-bilder](devtest-lab-configure-marketplace-images.md) – Azure DevTest Labs stöder skapandet av virtuella datorer baserat på Azure Marketplace-bilder. Den här artikeln visar hur du kan ange vilka eventuella Azure Marketplace-bilder kan användas när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm-with-artifacts.md) -illustrerar hur du skapar en virtuell dator från en grundläggande bild (antingen anpassad eller Marketplace), och hur du arbetar med artefakter i den virtuella datorn.

