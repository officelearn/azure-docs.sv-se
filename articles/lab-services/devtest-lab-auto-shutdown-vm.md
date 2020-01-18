---
title: Konfigurera inställningar för automatisk avstängning för en virtuell dator i Azure DevTest Labs
description: Lär dig hur du konfigurerar inställningar för automatisk avstängning för en virtuell dator (VM) så att den virtuella datorn stängs av automatiskt när den inte används.
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 4cca4fd4bc9cd880c5b7a75e54e8cfd8192bae1e
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170198"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Konfigurera inställningar för automatisk avstängning för en virtuell dator i Azure DevTest Labs
Med Azure DevTest Labs kan du kontrol lera kostnaderna och minimera spill i labbet genom att hantera principer (inställningar) för varje labb. Den här artikeln visar hur du konfigurerar principen för automatisk avstängning för ett labb konto och konfigurerar inställningar för automatisk avstängning för ett labb i labb kontot. Information om hur du anger alla labb principer finns [i definiera labb principer i Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="autoshutdown-policy-for-a-lab"></a>Princip för automatisk avstängning för ett labb
Som labb ägare kan du konfigurera ett avslutnings schema för alla virtuella datorer i labbet. Genom att göra det kan du spara kostnader från att köra datorer som inte används (inaktiva). Du kan tillämpa en avslutnings princip på alla dina virtuella labb datorer centralt men även spara dina labb användare för att ställa in ett schema för sina enskilda datorer. Med den här funktionen kan du ange principen för ditt labb schema med början från att ge dig ingen kontroll över fullständig behörighet till dina labb användare. Som labb ägare kan du konfigurera den här principen genom att utföra följande steg:

1. På Start sidan för ditt labb väljer du **konfiguration och principer**.
2. Välj **princip för automatisk avstängning** i avsnittet **scheman** på den vänstra menyn.
3. Välj ett av alternativen. I följande avsnitt får du mer information om de här alternativen: set-principen gäller endast för nya virtuella datorer som skapats i labbet och inte till redan befintliga virtuella datorer. 

    ![Princip alternativ för automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>Konfigurera inställningar för automatisk avstängning för ett labb
Principen för automatisk avstängning hjälper till att minimera labb avfall genom att göra det möjligt att ange den tid som Labbets virtuella datorer stängs av.

Följ dessa steg om du vill visa (och ändra) principerna för ett labb:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster**och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du önskat labb.   
4. Välj **konfiguration och principer**.

    ![Fönstret princip inställningar](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. I rutan **konfiguration och principer** för labb väljer du **Automatisk avstängning** under **scheman**.
   
    ![Automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Välj **på** för att aktivera den här principen och **Inaktivera för att** inaktivera den.
7. Om du aktiverar den här principen anger du tid (och tidszon) för att stänga av alla virtuella datorer i det aktuella labbet.
8. Ange **Ja** eller **Nej** om du vill skicka ett meddelande 15 minuter före den angivna tiden för automatisk avstängning. Om du väljer **Ja**anger du en webhook-URL-slutpunkt eller en e-postadress som anger var du vill att meddelandet ska publiceras eller skickas. Användaren får ett meddelande och det ges möjlighet att fördröja avstängningen. Mer information finns i avsnittet om [meddelanden](#notifications) . 
9. Välj **Spara**.

    Som standard gäller den här principen för alla virtuella datorer i det aktuella labbet. Om du vill ta bort den här inställningen från en speciell virtuell dator öppnar du hanterings fönstret för den virtuella datorn och ändrar inställningen för automatisk **avstängning** .

## <a name="configure-autoshutdown-settings-for-a-vm"></a>Konfigurera inställningar för automatisk avstängning för en virtuell dator

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Användare anger ett schema och kan välja
Om det här princip alternativet har ställts in för labbet kan användarna åsidosätta eller välja ur labb schema. Det här alternativet ger labb användare fullständig kontroll över schemat för automatisk avstängning av deras virtuella datorer. Labb användare ser ingen ändring på sidan schema för automatisk avstängning av virtuell dator.

![Princip alternativ för automatisk avstängning – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Användare anger ett schema och kan inte avanmäla
Om det här princip alternativet har ställts in för labbet kan användarna åsidosätta labb schemat. De kan dock inte välja bort principen för automatisk avstängning. Det här alternativet ser till att varje dator i labbet är under ett schema för automatisk avstängning. Labb användare kan uppdatera schemat för de virtuella datorerna automatiskt och konfigurera avslutnings meddelanden.

![Princip alternativ för automatisk avstängning – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Användaren har ingen kontroll över det schema som angetts av labb administratören
Om det här princip alternativet har ställts in för labbet kan användarna inte åsidosätta eller välja labb schema. Det här alternativet ger labb administratören fullständig kontroll av schemat för varje dator i labbet. Labb användare kan bara konfigurera meddelanden för automatisk avstängning för sina virtuella datorer.

![Princip alternativ för automatisk avstängning – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Meddelanden
När automatisk avstängning har kon figurer ATS av labb ägaren skickas meddelanden till labb användarna 15 minuter innan den automatiskt stängs av om någon av deras virtuella datorer påverkas. Det här alternativet ger labb användare möjlighet att spara sitt arbete innan den stängs av. Meddelandet innehåller också länkar för varje virtuell dator för följande åtgärder:

- Hoppa över automatisk avstängning för den här tiden
- Använd automatisk avstängning för en timme eller två timmar så att de kan fortsätta att arbeta på den virtuella datorn.

Meddelandet skickas via den konfigurerade webhook-slutpunkten eller en e-postadress som anges av labb ägare i inställningarna för automatisk avstängning. Med Webhooks kan du skapa eller konfigurera integreringar som prenumererar på vissa händelser. När en av dessa händelser aktive ras skickar DevTest Labs en HTTP POST-nytto Last till webhookens konfigurerade URL. Mer information om Webhooks finns i [skapa en webhook eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Vi rekommenderar att du använder Webhooks eftersom de stöds av olika appar (till exempel slack, Azure Logic Apps och så vidare) och gör att du kan använda ditt eget sätt för att skicka meddelanden. Ett exempel på att ta emot meddelanden om automatisk avstängning från e-postmeddelanden med hjälp av Azure Logic Apps finns i[skapa en logisk app som tar emot e-postmeddelanden](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications). 


## <a name="next-steps"></a>Nästa steg
Se [Hantera principer för automatisk avstängning för ett labb i Azure DevTest Labs](devtest-lab-auto-shutdown.md)
