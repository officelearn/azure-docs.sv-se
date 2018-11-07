---
title: Hantera principer för labbet i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du definierar principer för labbet, till exempel storlekar, maximalt virtuella datorer per användare och Stäng av automation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 378eb8c1f2070e8f4b28c221369938e2ff04e2f3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255190"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Hantera alla principer för ett labb i Azure DevTest Labs

Azure DevTest Labs kan du styra kostnader och minimera slöseri i dina labb genom att hantera principer (inställningar) för varje labb. Den här artikeln förklarar i detalj steg för steg hur du ställer in varje princip.  

## <a name="set-allowed-virtual-machine-sizes"></a>Ange tillåtna storlekar för virtuella datorer
Principen för att ställa in de tillåtna storlekarna hjälper till att minimera avfall lab genom att ange vilka VM-storlekar som tillåts i laboratoriet. Om den här principen är aktiverad kan användas endast de storlekar som gäller från den här listan för att skapa virtuella datorer.

1. I den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040), Välj ett labb och välj sedan **konfiguration och principer**.

    ![Använda testmiljön konfigurations- och principer](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. På testmiljön **konfiguration och principer** väljer **tillåtna storlekar för virtuella datorer**.
   
    ![Tillåtna VM-storlekar](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Välj **på** att aktivera den här principen och **av** att inaktivera den.

1. Om du aktiverar den här principen väljer du en eller flera VM-storlekar som kan skapas i labbet.

1. Välj **Spara**.

## <a name="set-virtual-machines-per-user"></a>Virtuella datorer per användare
Principen för **virtuella datorer per användare** kan du ange det maximala antalet virtuella datorer som kan skapas med en enskild användare. Om en användare försöker skapa eller gör anspråk på en virtuell dator när gränsen för textmeddelandeanvändare har uppfyllts, ett felmeddelande som anger att den virtuella datorn inte kan skapas/ägs. 

1. På testmiljön **konfiguration och principer** väljer **virtuella datorer per användare**.
   
    ![Virtuella datorer per användare](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Välj **Ja** att begränsa antalet virtuella datorer per användare. Om du inte vill begränsa antalet virtuella datorer per användare väljer **nr**. Om du väljer **Ja**, ange ett numeriskt värde som anger det maximala antalet virtuella datorer som kan skapas eller ägs av en användare. 

1. Välj **Ja** att begränsa antalet virtuella datorer som kan använda SSD (Solid State disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD, väljer **nr**. Om du väljer **Ja**, ange ett värde som anger det maximala antalet virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-virtual-machines-per-lab"></a>Virtuella datorer per labb
Principen för **virtuella datorer per labb** kan du ange det maximala antalet virtuella datorer som kan skapas för den aktuella testmiljön. Om en användare försöker skapa en virtuell dator när lab gränsen har uppfyllts, ett felmeddelande som anger att det inte går att skapa den virtuella datorn. 

1. På testmiljön **konfiguration och principer** väljer **virtuella datorer per labb**.
   
    ![Virtuella datorer per labb](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Välj **Ja** att begränsa antalet virtuella datorer per labb. Om du inte vill begränsa antalet virtuella datorer per labb väljer **nr**. Om du väljer **Ja**, ange ett numeriskt värde som anger det maximala antalet virtuella datorer som kan skapas eller ägs av en användare. 

1. Välj **Ja** att begränsa antalet virtuella datorer som kan använda SSD (Solid State disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD, väljer **nr**. Om du väljer **Ja**, ange ett värde som anger det maximala antalet virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-auto-shutdown"></a>Ställ in automatisk avstängning
Princip för automatisk avstängning hjälper till att minimera lab avfall genom att du kan ange hur lång tid som den här övningen virtuella datorer stängs av.

1. På testmiljön **konfiguration och principer** väljer **automatisk avstängning**.
   
    ![Automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Välj **på** att aktivera den här principen och **av** att inaktivera den.

1. Om du aktiverar den här principen kan du ange tid (och tidszon) för att stänga av alla virtuella datorer i den aktuella testmiljön.

1. Ange **Ja** eller **nr** för alternativet att skicka en avisering 15 minuter innan angivna automatisk avstängning. Om du väljer **Ja**, ange en webhook-URL-slutpunkt eller en e-postadress som anger var du vill att meddelanden ska lagt in eller skickas. Användaren får ett meddelande och ges möjlighet att skjuta avstängningen.

   Läs mer om webhooks [skapa en webhook eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Välj **Spara**.

Som standard när du har aktiverat, den här principen gäller för alla virtuella datorer i den aktuella testmiljön. Om du vill ta bort den här inställningen från en specifik virtuell dator, öppna fönstret för hantering av den virtuella datorn och ändra dess **automatisk avstängning** inställningen.

## <a name="set-auto-start"></a>Ställ in automatisk start
Princip för automatisk start kan du ange när de virtuella datorerna i den aktuella testmiljön ska startas.  

1. På testmiljön **konfiguration och principer** väljer **automatisk start**.
   
    ![Starta automatiskt](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Välj **på** att aktivera den här principen och **av** att inaktivera den.

3. Om du aktiverar den här principen kan du ange den schemalagda starttiden, tidszon och veckodagar som gäller tiden. 

4. Välj **Spara**.

När du har aktiverat, tillämpas inte den här principen automatiskt till alla virtuella datorer i den aktuella testmiljön. Om du vill tillämpa den här inställningen på en specifik virtuell dator, öppna fönstret för hantering av den virtuella datorn och ändra dess **automatisk start** inställningen.

## <a name="set-expiration-date"></a>Ange förfallodatum
Du kan ange en giltighetstid datum när du [skapa den virtuella datorn](devtest-lab-add-vm.md). I **avancerade inställningar**, Välj kalenderikonen för att ange ett datum som den virtuella datorn automatiskt bort. Som standard den virtuella datorn aldrig upphör att gälla.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har definierat och tillämpas olika VM-principinställningarna för labbet, är här några saker du kan prova sedan:

* [Förstå delade IP-adresser](devtest-lab-shared-ip.md) -förklarar hur delade IP-adresserna används i DevTest Labs för att minimera antalet offentliga IP-adresser som krävs för att ansluta till ditt labb virtuella datorer.
* [Konfigurera kostnadshantering](devtest-lab-configure-cost-management.md) -illustrerar hur du använder den **månatliga uppskattade kostnaden Trend** diagram  
  Om du vill visa den aktuella månaden uppskattade kostnad hittills och kostnadsprognoser i slutet av månaden.
* [Skapa en anpassad avbildning](devtest-lab-create-template.md) – när du skapar en virtuell dator kan du ange en bastabell som kan vara en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln visar hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) – Azure DevTest Labs stöder skapandet av virtuella datorer baserat på Azure Marketplace-avbildningar. Den här artikeln visar hur du kan ange vilka eventuella Azure Marketplace-avbildningar kan vara används när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm.md) -illustrerar hur du skapar en virtuell dator från en grundläggande avbildning (antingen anpassad eller Marketplace), och hur du arbetar med artefakter i den virtuella datorn.

