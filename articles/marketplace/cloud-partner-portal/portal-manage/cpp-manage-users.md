---
title: Hantera användare på Cloud Partner Portal | Azure Marketplace
description: Hantera användare på Cloud Partner Portal
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
manager: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: pabutler
ms.openlocfilehash: 4dc9fdf94ba6311f1b59c61ec686628a728e2510
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826640"
---
# <a name="managing-users-on-the-cloud-partner-portal"></a>Hantera användare på Cloud Partner Portal

Med [Cloud Partner Portal](https://cloudpartner.azure.com) kan du lägga till användare med rollbaserad åtkomst till erbjudanden för virtuella datorer. Det hjälper dig att hantera åtkomst och behörigheter samtidigt som flera personer arbetar med erbjudanden.

## <a name="add-users-and-assign-roles"></a>Lägg till användare och tilldela roller 

Använd följande steg för att lägga till användare och tilldela roller för erbjudande publicering.

1. Klicka på fliken **användare** i navigerings fönstret till vänster.

    ![Fliken användare](./media/userstab.png)


2. Klicka på **Lägg till användare**.

    ![Välj Lägg till användare](./media/adduser.png)


3. Skriv användarens e-postadress och välj en roll tilldelning.  Du kan lägga till den nya användaren som "ägare" eller "deltagare".

    **E-post**: Lägg till e-postadresserna till de grupp medlemmar som ska arbeta med publiceringen av erbjudandet. Microsoft-konton (Outlook, Hotmail och Live) och organisations-ID: n stöds.

    - Lägg till ett "grupp/grupp-e-postalias/säkerhets grupp" för att skydda mot den person som lämnar organisationen.
    - Se till att e-post-ID: n som anges på Cloud Partner Portal övervakas för kommunikation från Microsoft.
    
    ![Tilldela roll](./media/assignrole.png)

    **Roll**: referera till tabellen nedan för att identifiera rätt typ av användar roll.

    ![Roll nivå](./media/roleaccesslevel.png)

    Endast ägare har åtkomst till Azure-utbetalningar och Azure-kundflikar i [säljare insikter](../../cloud-partner-portal-orig/si-getting-started.md).


4. Välj det utgivar namn som du vill lägga till den nya användaren och klicka sedan på Lägg till för att slutföra användar tillägget.

    > [!NOTE]
    > "Utgivar namn" som din e-post läggs till som ägare eller deltagare visas i list rutan **för att välja** .


    ![Tilldela roll-Välj Utgivare](./media/assignselectpublisher.png)


## <a name="delete-an-existing-user"></a>Ta bort en befintlig användare

Om du vill ta bort en befintlig användare söker du efter e-postmeddelandet på fliken **användare** och trycker sedan på knappen **ta bort** på den högra sidan.


## <a name="next-steps"></a>Nästa steg

Du kan också använda portalen för att [Hantera utgivar profiler](./cpp-manage-publisher-profile.md).
