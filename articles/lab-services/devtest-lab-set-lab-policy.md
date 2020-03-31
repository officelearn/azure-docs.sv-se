---
title: Hantera labbprinciper i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du definierar labbprinciper som VM-storlekar, maximala virtuella datorer per användare och automatisering av avstängning.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270724"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Hantera alla principer för ett labb i Azure DevTest Labs

Med Azure DevTest Labs kan du styra kostnaderna och minimerar avfall i dina labb genom att hantera principer (inställningar) för varje labb. I den här artikeln beskrivs steg-för-steg-information om hur du anger varje princip.  

## <a name="set-allowed-virtual-machine-sizes"></a>Ange tillåtna storlekar för virtuella datorer
Principen för att ange tillåtna vm-storlekar hjälper till att minimera labbavfall genom att du kan ange vilka vm-storlekar som är tillåtna i labbet. Om den här principen är aktiverad kan endast vm-storlekar från den här listan användas för att skapa virtuella datorer.

1. I [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040)väljer du ett labb och väljer sedan **Konfiguration och principer**.

    ![Få tillgång till labbets konfiguration och principer](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. I fönstret **Konfiguration och principer** i labbet väljer du **Tillåtna storlekar för virtuella datorer**.
   
    ![Tillåtna storlekar för virtuella datorer](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Välj **På** om du vill aktivera den här principen och **Av** för att inaktivera den.

1. Om du aktiverar den här principen väljer du en eller flera VM-storlekar som kan skapas i labbet.

1. Välj **Spara**.

## <a name="set-virtual-machines-per-user"></a>Ange virtuella datorer per användare
Med principen för **virtuella datorer per användare** kan du ange hur många virtuella datorer som kan skapas av en enskild användare. Om en användare försöker skapa eller göra anspråk på en virtuell dator när användargränsen har uppfyllts, anger ett felmeddelande att den virtuella datorn inte kan skapas/begäras. 

1. I fönstret **Konfiguration och principer** i labbet väljer du Virtuella datorer per **användare**.
   
    ![Virtuella datorer per användare](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Välj **Ja** om du vill begränsa antalet virtuella datorer per användare. Om du inte vill begränsa antalet virtuella datorer per användare väljer du **Nej**. Om du väljer **Ja**anger du ett numeriskt värde som anger antalet virtuella datorer som kan skapas eller begäras av en användare. 

1. Välj **Ja** om du vill begränsa antalet virtuella datorer som kan använda SSD (solid-state disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD väljer du **Nej**. Om du väljer **Ja**anger du ett värde som anger antalet virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-virtual-machines-per-lab"></a>Ange virtuella datorer per labb
Principen för **virtuella datorer per labb** kan du ange antalet virtuella datorer som kan skapas för det aktuella labbet. Om en användare försöker skapa en virtuell dator när labbgränsen har uppfyllts, anger ett felmeddelande att den virtuella datorn inte kan skapas. 

1. I fönstret **Konfiguration och principer** i labbet väljer du Virtuella datorer per **labb**.
   
    ![Virtuella datorer per labb](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Välj **Ja** om du vill begränsa antalet virtuella datorer per labb. Om du inte vill begränsa antalet virtuella datorer per labb väljer du **Nej**. Om du väljer **Ja**anger du ett numeriskt värde som anger antalet virtuella datorer som kan skapas eller begäras av en användare. 

1. Välj **Ja** om du vill begränsa antalet virtuella datorer som kan använda SSD (solid-state disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD väljer du **Nej**. Om du väljer **Ja**anger du ett värde som anger antalet virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-auto-shutdown"></a>Ange automatisk avstängning
Principen för automatisk avstängning hjälper till att minimera labbavfall genom att låta dig ange den tid som det här labbets virtuella datorer stängs av.

1. I fönstret **Konfiguration och principer** i labbet väljer du Automatisk **avstängning**.
   
    ![Automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Välj **På** om du vill aktivera den här principen och **Av** för att inaktivera den.

1. Om du aktiverar den här principen anger du vilken tid (och tidszon) som ska stängas av alla virtuella datorer i det aktuella labbet.

1. Ange **Ja** eller **Nej** om du vill skicka ett meddelande 15 minuter före den angivna automatiska avstängningstiden. Om du väljer **Ja**anger du en webhook-URL-slutpunkt eller en e-postadress som anger var du vill att meddelandet ska bokföras eller skickas. Användaren får ett meddelande och får möjlighet att skjuta upp avstängningen.

   Mer information om webhooks finns i [Skapa en webhook- eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Välj **Spara**.

Som standard gäller den här principen för alla virtuella datorer i det aktuella labbet när den är aktiverad. Om du vill ta bort den här inställningen från en viss virtuell dator öppnar du hanteringsfönstret för den virtuella datorn och ändrar dess inställning **för automatisk avstängning.**

## <a name="set-auto-shutdown-policy"></a>Ange princip för automatisk avstängning
Som labbägare kan du konfigurera ett avstängningsschema för alla virtuella datorer i labbet. På så sätt kan du spara kostnader från att köra datorer som inte används (inaktiv). Du kan tillämpa en avstängningsprincip för alla virtuella labb-datorer centralt men också spara labbanvändarna från att konfigurera ett schema för sina enskilda datorer. Med den här funktionen kan du ställa in principen på ditt labbschema från att erbjuda ingen kontroll till fullständig kontroll, till dina labbanvändare. Som labbägare kan du konfigurera den här principen genom att vidta följande åtgärder:

1. På startsidan för labbet väljer du **Konfiguration och principer**.
2. Välj **automatisk avstängningsprincip** i avsnittet **Scheman** på den vänstra menyn.
3. Välj ett av alternativen. I följande avsnitt får du mer information om dessa alternativ: Principen gäller endast för nya virtuella datorer som skapats i labbet och inte för de redan befintliga virtuella datorerna. 

    ![Alternativ för automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Användaren anger ett schema och kan välja bort
Om du ställer in labbet på den här principen kan labbanvändarna åsidosätta eller välja bort labbschemat. Det här alternativet ger labbanvändare full kontroll över schemat för automatisk avstängning av sina virtuella datorer. Labbanvändare ser ingen ändring i sin sida för automatisk avstängning av virtuella datorer.

![Alternativ för automatisk avstängning - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Användaren anger ett schema och kan inte välja bort
Om du ställer in labbet på den här principen kan labbanvändare åsidosätta labbschemat. De kan dock inte välja bort automatisk avstängningsprincip. Det här alternativet ser till att alla datorer i labbet är under ett schema för automatisk avstängning. Labbanvändare kan uppdatera schemat för automatisk avstängning av sina virtuella datorer och konfigurera avstängningsmeddelanden.

![Alternativ för automatisk avstängning - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Användaren har ingen kontroll över schemat som fastställts av labbadministratör
Om du ställer in labbet på den här principen kan labbanvändare inte åsidosätta eller välja bort labbschemat. Det här alternativet ger labbadministratören fullständig kontroll på schemat för varje dator i labbet. Labbanvändare kan bara ställa in meddelanden om automatisk avstängning för sina virtuella datorer.

![Alternativ för automatisk avstängning - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Ange automatisk start
Med principen för automatisk start kan du ange när de virtuella datorerna i det aktuella labbet ska startas.  

1. I fönstret **Konfiguration och principer** i labbet väljer du Starta **automatiskt**.
   
    ![Starta automatiskt](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Välj **På** om du vill aktivera den här principen och **Av** för att inaktivera den.

3. Om du aktiverar den här principen anger du den schemalagda starttiden, tidszonen och veckodagarna som tiden gäller för. 

4. Välj **Spara**.

När den här principen har aktiverats tillämpas den här principen inte automatiskt på några virtuella datorer i det aktuella labbet. Om du vill använda den här inställningen på en viss virtuell dator öppnar du hanteringsfönstret för den virtuella datorn och ändrar inställningen **För automatisk start.**

## <a name="set-expiration-date"></a>Ange utgångsdatum
Du kan ange ett förfallodatum när du [skapar den virtuella datorn](devtest-lab-add-vm.md). I **Avancerade inställningar**väljer du kalenderikonen för att ange ett datum då den virtuella datorn tas bort automatiskt. Som standard upphör den virtuella datorn aldrig att gälla.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har definierat och tillämpat de olika vm-principinställningarna för ditt labb kan du prova följande:

* [Förstå delade IP-adresser](devtest-lab-shared-ip.md) - Förklarar hur delade IP-adresser används i DevTest Labs för att minimera antalet offentliga IP-adresser som krävs för att ansluta till dina virtuella labb-datorer.
* [Konfigurera kostnadshantering](devtest-lab-configure-cost-management.md) - Visar hur du använder diagrammet **Månatlig beräknad kostnadstrend**  
  om du vill visa den aktuella månadens uppskattade hittillskostnad och den beräknade kostnaden vid månadsutgången.
* [Skapa anpassad avbildning](devtest-lab-create-template.md) – När du skapar en virtuell dator anger du en bas som kan vara antingen en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln illustrerar hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) – Azure DevTest Labs stöder att skapa virtuella datorer baserat på Azure Marketplace-avbildningar. Den här artikeln visar hur du anger vilka, om några, Azure Marketplace-avbildningar som kan användas när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm.md) - Illustrerar hur du skapar en virtuell dator från en basavbildning (antingen anpassad eller Marketplace) och hur du arbetar med artefakter i den virtuella datorn.

