---
title: Så här lägger du till ytterligare ägare i ett labb i Azure Lab Services
description: Den här artikeln visar hur en administratör kan lägga till en användare som ägare i ett labb i Azure Lab Services.
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
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443524"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Så här lägger du till ytterligare ägare i ett befintligt labb i Azure Lab Services
Den här artikeln visar hur du som administratör kan lägga till ytterligare ägare i ett befintligt labb.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Lägga till användare i läsarrollen för labbkontot
Om du vill lägga till en användare som ytterligare ägare **read** i ett befintligt labb måste du först ge användaren läsbehörighet för labbkontot.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Sök efter **Lab Services**och välj det sedan.
3. Välj ditt **labbkonto** i listan. 
2. På **sidan Labbkonto**väljer du **Åtkomstkontroll (IAM)** på den vänstra menyn. 
2. På sidan **Access control (IAM)** väljer du **Lägg till** i verktygsfältet och väljer Lägg **till rolltilldelning**.

    ![Rolltilldelning för labbkontot ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Gör följande på sidan **Lägg till en rolltilldelning:** 
    1. Välj **Läsare** för **rollen**. 
    2. Välj användaren. 
    3. Välj **Spara**. 

        ![Lägga till användare i läsarrollen för labbkontot ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Lägga till användare i ägarrollen för labbet

1. Tillbaka på sidan **Labbkonto** väljer du **Alla labb** på den vänstra menyn.
2. Välj det **labb** som du vill lägga till användare som ägare till. 
    
    ![Välja labbet ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. På **labbsidan** väljer du **Access-kontroll (IAM)** på den vänstra menyn.
4. På sidan **Access control (IAM)** väljer du **Lägg till** i verktygsfältet och väljer Lägg **till rolltilldelning**.
5. Gör följande på sidan **Lägg till en rolltilldelning:** 
    1. Välj **Ägare** för **rollen**. 
    2. Välj användaren. 
    3. Välj **Spara**. 

## <a name="next-steps"></a>Nästa steg
Bekräfta att användaren ser labbet när du loggar in på [Lab Services-portalen](https://labs.azure.com).