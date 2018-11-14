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
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622031"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Hantera alla principer för ett labb i Azure DevTest Labs

Azure DevTest Labs kan du styra kostnader och minimerar spilltid i samband med dina labb genom att hantera principer (inställningar) för varje labb. Den här artikeln förklarar i detalj steg för steg hur du ställer in varje princip.  

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
Principen för **virtuella datorer per användare** kan du ange hur många virtuella datorer som kan skapas av en enskild användare. Om en användare försöker skapa eller gör anspråk på en virtuell dator när gränsen för textmeddelandeanvändare har uppfyllts, ett felmeddelande som anger att den virtuella datorn inte kan skapas/ägs. 

1. På testmiljön **konfiguration och principer** väljer **virtuella datorer per användare**.
   
    ![Virtuella datorer per användare](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Välj **Ja** att begränsa antalet virtuella datorer per användare. Om du inte vill begränsa antalet virtuella datorer per användare väljer **nr**. Om du väljer **Ja**, ange ett numeriskt värde som anger hur många virtuella datorer som kan skapas eller ägs av en användare. 

1. Välj **Ja** att begränsa antalet virtuella datorer som kan använda SSD (Solid State disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD, väljer **nr**. Om du väljer **Ja**, ange ett värde som anger hur många virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-virtual-machines-per-lab"></a>Virtuella datorer per labb
Principen för **virtuella datorer per labb** kan du ange hur många virtuella datorer som kan skapas för den aktuella testmiljön. Om en användare försöker skapa en virtuell dator när lab gränsen har uppfyllts, ett felmeddelande som anger att det inte går att skapa den virtuella datorn. 

1. På testmiljön **konfiguration och principer** väljer **virtuella datorer per labb**.
   
    ![Virtuella datorer per labb](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Välj **Ja** att begränsa antalet virtuella datorer per labb. Om du inte vill begränsa antalet virtuella datorer per labb väljer **nr**. Om du väljer **Ja**, ange ett numeriskt värde som anger hur många virtuella datorer som kan skapas eller ägs av en användare. 

1. Välj **Ja** att begränsa antalet virtuella datorer som kan använda SSD (Solid State disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD, väljer **nr**. Om du väljer **Ja**, ange ett värde som anger hur många virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-auto-shutdown"></a>Ställ in automatisk avstängning
Principen för automatisk avstängning hjälper till att minimera lab avfall genom att du kan ange hur lång tid som den här övningen virtuella datorer stängs av.

1. På testmiljön **konfiguration och principer** väljer **automatisk avstängning**.
   
    ![Automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Välj **på** att aktivera den här principen och **av** att inaktivera den.

1. Om du aktiverar den här principen kan du ange tid (och tidszon) för att stänga av alla virtuella datorer i den aktuella testmiljön.

1. Ange **Ja** eller **nr** för alternativet att skicka en avisering 15 minuter före den angivna automatisk avstängning av tiden. Om du väljer **Ja**, ange en webhook-URL-slutpunkt eller en e-postadress som anger var du vill att meddelanden ska lagt in eller skickas. Användaren får ett meddelande och ges möjlighet att skjuta upp avstängningen.

   Läs mer om webhooks [skapa en webhook eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Välj **Spara**.

Som standard när du har aktiverat, den här principen gäller för alla virtuella datorer i den aktuella testmiljön. Om du vill ta bort den här inställningen från en specifik virtuell dator, öppna fönstret för hantering av den virtuella datorn och ändra dess **automatisk avstängning** inställningen.

## <a name="set-auto-shutdown-policy"></a>Ange princip för automatisk avstängning
Som labbägare kan konfigurera du ett schema för avstängning för alla virtuella datorer i labbet. Du kan spara pengar från datorer som inte används körs (viloläge) genom att göra detta. Du kan tillämpa en princip för avstängning på alla labbet virtuella datorer centralt utan även spara din labbanvändare arbetet från att konfigurera ett schema för deras enskilda datorer. Den här funktionen kan du ställa in principen på ditt labb-schema med början från erbjuder ingen kontroll till fullständig behörighet till dina labbanvändare. Som labbägare kan konfigurera du den här principen genom att utföra följande steg:

1. På startsidan för labbet, väljer **konfiguration och principer**.
2. Välj **automatisk avstängning princip** i den **scheman** avsnitt i den vänstra menyn.
3. Välj något av alternativen. I följande avsnitt innehåller mer information om de här alternativen: set-principen gäller endast för nya virtuella datorer som skapas i labbet och inte för redan befintliga virtuella datorer. 

    ![Alternativ för automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Användaren anger ett schema och kan avanmäla dig
Om du anger ditt labb till den här principen kan labbanvändare åsidosätta eller välja bort lab-schema. Det här alternativet ger labbanvändare fullständig kontroll över schemat för automatisk avstängning av sina virtuella datorer. Lab användarna ser ingen ändring i sina schemasidan för VM automatisk avstängning.

![Automatisk avstängning principalternativ - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Användaren anger ett schema och det går inte att avanmäla dig
Om du anger ditt labb till den här principen kan labbanvändare åsidosätta lab-schema. Men kan inte de välja bort principen för automatisk avstängning. Det här alternativet ser till att alla virtuella datorer i labbet är under ett schema för automatisk avstängning. Labbanvändare kan uppdatera schemat för automatisk avstängning av sina virtuella datorer och konfigurera avslutning av meddelanden.

![Automatisk avstängning principalternativ - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Användaren har ingen kontroll över det schema som angetts av administratören för labb
Om du anger ditt labb till den här principen kan inte labbanvändare åsidosätta eller välja bort lab-schema. Det här alternativet erbjuder lab admin fullständig kontroll över schemat för varje dator i labbet. Labbanvändare kan bara ställa in automatisk avstängning meddelanden för sina virtuella datorer.

![Automatisk avstängning principalternativ - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Ange autostart
Autostart-principen kan du ange när de virtuella datorerna i den aktuella testmiljön ska startas.  

1. På testmiljön **konfiguration och principer** väljer **Autostart**.
   
    ![Starta automatiskt](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Välj **på** att aktivera den här principen och **av** att inaktivera den.

3. Om du aktiverar den här principen kan du ange den schemalagda starttiden, tidszon och veckodagar som gäller tiden. 

4. Välj **Spara**.

När du har aktiverat, tillämpas inte den här principen automatiskt till alla virtuella datorer i den aktuella testmiljön. Om du vill tillämpa den här inställningen på en specifik virtuell dator, öppna fönstret för hantering av den virtuella datorn och ändra dess **Autostart** inställningen.

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

