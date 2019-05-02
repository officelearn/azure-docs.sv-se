---
title: Hantera användare på partnerportalen i molnet | Azure Marketplace
description: Hantera användare på partnerportalen i molnet
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
manager: pbutlerm
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: pabutler
ms.openlocfilehash: 21c49ad0d73721dd204a7a3899d11cf10d43521b
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942087"
---
# <a name="managing-users-on-the-cloud-partner-portal"></a>Hantera användare på partnerportalen i molnet

Den [Cloud Partner Portal](https://cloudpartner.azure.com) kan du lägga till användare med rollbaserad åtkomst till virtuell dator-erbjudanden. Det hjälper dig för att hantera åtkomst och behörighet när flera personer arbetar med erbjudanden.

## <a name="add-users-and-assign-roles"></a>Lägga till användare och tilldela roller 

Använd följande steg för att lägga till användare och tilldela roller för publicering av erbjudandet.

1. Klicka på den **användare** fliken i navigeringsfönstret till vänster.

    ![Fliken användare](./media/userstab.png)


2. Klicka på **Lägg till användare**.

    ![Välj Lägg till användare](./media/adduser.png)


3. Ange användarens e-postadress och välj en rolltilldelning.  Du kan lägga till den nya användaren som ”ägare” eller ”bidragsgivare”.

    **e-post**: Lägg till e-postadresserna för gruppmedlemmar som kommer att arbeta med publicera erbjudandet. Microsoft-konton (Outlook, Hotmail och Live) och organisation-ID: N stöds.

    - Lägg till en ”team/grupp e-alias/säkerhetsgrupp” som skydd mot den person som lämnar organisationen.
    - Se till att e-ID: N i Cloud Partner Portal övervakas för all kommunikation från Microsoft.
    
    ![Tilldela roll](./media/assignrole.png)

    **Roll**: Se tabellen nedan för att identifiera rolltypen lämpliga användare.

    ![Roller nivå](./media/roleaccesslevel.png)

    Endast ägare kan komma åt Azure Payouts och Azure-kund flikarna i [försäljning Insights](../../cloud-partner-portal-orig/si-getting-started.md).


4. Utgivarens namn som du vill lägga till den nya användaren och sedan klicka på Lägg till för att slutföra användaren tillägg.

    > [!NOTE]
    > ”Utgivarnamnen” som din e-post har lagts till som ägare eller deltagare visas i listrutan för **Publisher** väljas.


    ![Tilldela roll - väljer utgivare](./media/assignselectpublisher.png)


## <a name="delete-an-existing-user"></a>Ta bort en befintlig användare

Ta bort en befintlig användare genom att söka efter sin e-post i den **användare** fliken och tryck sedan på den **ta bort** knappen på höger sida.


## <a name="next-steps"></a>Nästa steg

Du kan också använda portalen för att [hantera publisher profiler](./cpp-manage-publisher-profile.md).
