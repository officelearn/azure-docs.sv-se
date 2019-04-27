---
title: Hantera grundläggande labbprinciper i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du ställer in några av de grundläggande principerna (inställningar) för ett labb i DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cc529fbf9b24335be1bec07f81c732ced7a2b72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773850"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Hantera grundläggande principer för ett labb i Azure DevTest Labs

Azure DevTest Labs kan du styra kostnader och minimera slöseri i dina labb genom att hantera principer (inställningar) för varje labb. I den här artikeln får komma du igång med principer genom att lära dig hur du anger två av de viktigaste principerna – begränsning av antalet virtuella datorer (VM) som kan skapas eller ägs av en enskild användare och konfigurera automatisk avstängning. Information om hur du ställer in varje labb-princip finns i [definiera labbprinciper i Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Åtkomst till ett labb principer i Azure DevTest Labs
Följande steg vägleder dig genom att ställa in principer för ett labb i Azure DevTest Labs:

Om du vill visa och ändra principer för ett labb, Följ dessa steg:

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.

1. Listan över labbar, Välj önskade labbet.   

1. Välj **konfiguration och principer**.

    ![Rutan inställningar för principen](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Den **konfiguration och principer** fönstret innehåller en meny med inställningar som du kan ange. Den här artikeln beskriver bara till inställningarna för **virtuella datorer per användare**, **automatisk avstängning**, och **automatisk start**. Mer information om återstående inställningar, se [hantera alla principer för ett labb i Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Virtuella datorer per användare
Principen för **virtuella datorer per användare** kan du ange det maximala antalet virtuella datorer som kan skapas med en enskild användare. Om en användare försöker skapa eller gör anspråk på en virtuell dator när gränsen för textmeddelandeanvändare har uppfyllts, ett felmeddelande som anger att den virtuella datorn inte kan skapas/ägs. 

1. På testmiljön **konfiguration och principer** menyn och välj **virtuella datorer per användare**.
   
    ![Virtuella datorer per användare](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Välj **Ja** att begränsa antalet virtuella datorer per användare. Om du inte vill begränsa antalet virtuella datorer per användare väljer **nr**. Om du väljer **Ja**, ange ett numeriskt värde som anger det maximala antalet virtuella datorer som kan skapas eller ägs av en användare. 

1. Välj **Ja** att begränsa antalet virtuella datorer som kan använda SSD (Solid State disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD, väljer **nr**. Om du väljer **Ja**, ange ett värde som anger det maximala antalet virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-auto-shutdown"></a>Ställ in automatisk avstängning
Princip för automatisk avstängning hjälper till att minimera lab avfall genom att du kan ange hur lång tid som den här övningen virtuella datorer stängs av.

1. På testmiljön **konfiguration och principer** väljer **automatisk avstängning**.
   
    ![Automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Välj **på** att aktivera den här principen och **av** att inaktivera den.

1. Om du aktiverar den här principen kan du ange tid (och tidszon) för att stänga av alla virtuella datorer i den aktuella testmiljön.

1. Ange **Ja** eller **nr** för alternativet att skicka en avisering 15 minuter innan angivna automatisk avstängning. Om du väljer **Ja**anger en webhook-URL-slutpunkt eller e-postadressen för att ange var du vill att meddelanden ska lagt in eller skickas. Användaren får ett meddelande och ges möjlighet att skjuta avstängningen.

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

När du har aktiverat, tillämpas inte den här principen automatiskt till alla virtuella datorer i den aktuella testmiljön. Om du vill tillämpa den här inställningen på en befintlig virtuell dator, öppna fönstret för hantering av den virtuella datorn och ändra dess **automatisk start** inställningen.

## <a name="next-steps"></a>Nästa steg

- [Definiera labbprinciper i Azure DevTest Labs](devtest-lab-set-lab-policy.md) – Lär dig hur du ändrar andra principer för labbet.
