---
title: Så här lägger du till ytterligare ägare till ett labb i Azure Lab Services
description: Den här artikeln visar hur en administratör kan lägga till en användare som ägare till ett labb i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 1438c9c4f33cca45b6c609e90cc2cbe7a762adb0
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897357"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Så här lägger du till ytterligare ägare till ett befintligt labb i Azure Lab Services
Den här artikeln visar hur du, som administratör, kan lägga till ytterligare ägare till ett befintligt labb.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Lägg till användare till rollen läsare för labb kontot
Om du vill lägga till en användare som ytterligare ägare till ett befintligt labb måste du först ge användaren **Läs** behörighet till labb kontot.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Sök efter **labb tjänster**och markera det sedan.
3. Välj ditt **labb konto** i listan. 
2. På **sidan labb konto**väljer du **Access Control (IAM)** på den vänstra menyn. 
2. Välj **Lägg till** i verktygsfältet på sidan **åtkomst kontroll (IAM)** och välj **Lägg till roll tilldelning**.

    ![Roll tilldelning för labb kontot ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Utför följande steg på sidan **Lägg till en roll tilldelning** : 
    1. Välj **läsare** för **rollen**. 
    2. Välj användaren. 
    3. Välj **Spara**. 

        ![Lägg till användare till rollen läsare för labb kontot ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Lägg till användare i ägar rollen för labbet

1. Gå tillbaka till **labb konto** sidan, Välj **alla labb** på den vänstra menyn.
2. Välj det **labb** som du vill lägga till en användare till som en ägare. 
    
    ![Välja labbet ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. På sidan **labb** väljer du **åtkomst kontroll (IAM)** på den vänstra menyn.
4. Välj **Lägg till** i verktygsfältet på sidan **åtkomst kontroll (IAM)** och välj **Lägg till roll tilldelning**.
5. Utför följande steg på sidan **Lägg till en roll tilldelning** : 
    1. Välj **ägare** för **rollen**. 
    2. Välj användaren. 
    3. Välj **Spara**. 

## <a name="next-steps"></a>Nästa steg
Bekräfta att användaren ser labbet när de loggar in på [labb tjänst portalen](https://labs.azure.com).