---
title: Så här lägger du till användare som labb ägare i Azure Lab Services
description: Den här artikeln visar hur en administratör kan lägga till en användare som ägare till ett labb.
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
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 9e007ee6c95843b7e8ef9d7a701237034811489e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480858"
---
# <a name="how-to-add-a-user-as-an-owner-of-a-classroom-lab-in-azure-lab-services"></a>Så här lägger du till en användare som ägare till ett klass rums labb i Azure Lab Services
Den här artikeln visar hur du lägger till en användare som ägare till ett labb i Azure Lab Services.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Lägg till användare till rollen läsare för labb kontot
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Sök efter **labb tjänster**och markera det sedan.
3. Välj ditt **labb konto** i listan. 
2. På **sidan labb konto**väljer du **Access Control (IAM)** på den vänstra menyn. 
2. Välj **Lägg till** i verktygsfältet på sidan **åtkomst kontroll (IAM)** och välj **Lägg till roll tilldelning**.

    ![Roll tilldelning för labb kontot ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Utför följande steg på sidan **Lägg till en roll tilldelning** : 
    1. Välj **läsare** för **rollen**. 
    2. Välj användaren. 
    3. Välj **Spara**. 

        ![Lägg till användare till rollen läsare för labb kontot ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Lägg till användare i ägar rollen för labbet

1. Gå tillbaka till **labb konto** sidan, Välj **alla labb** på den vänstra menyn.
2. Välj det **labb** som du vill lägga till en användare till som en ägare. 
    
    ![Välja labbet ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. På sidan **labb** väljer du **åtkomst kontroll (IAM)** på den vänstra menyn.
4. Välj **Lägg till** i verktygsfältet på sidan **åtkomst kontroll (IAM)** och välj **Lägg till roll tilldelning**.
5. Utför följande steg på sidan **Lägg till en roll tilldelning** : 
    1. Välj **ägare** för **rollen**. 
    2. Välj användaren. 
    3. Välj **Spara**. 

## <a name="next-steps"></a>Nästa steg
Bekräfta att användaren ser labbet när de loggar in på [labb tjänst portalen](https://labs.azure.com).