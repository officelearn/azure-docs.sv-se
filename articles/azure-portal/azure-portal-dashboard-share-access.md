---
title: Dela Azure Portal-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll
description: I den här artikeln beskrivs hur du delar en instrumentpanel i Azure-portalen med hjälp av rollbaserad åtkomstkontroll.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 711e8a7ae31888c9754252d88404d90f24e8030e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131986"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll

När du har konfigurerat en instrumentpanel kan du publicera den och dela den med andra användare i organisationen. Du tillåter andra att visa instrumentpanelen med hjälp av Azure [Role-Based Access Control](../role-based-access-control/role-assignments-portal.md) (RBAC). Tilldela en användare eller grupp av användare till en roll. Den rollen definierar om dessa användare kan visa eller ändra den publicerade instrumentpanelen.

Alla publicerade instrumentpaneler implementeras som Azure-resurser. De finns som hanterbara artiklar i din prenumeration och finns i en resursgrupp. Ur ett åtkomstkontrollperspektiv skiljer sig inte instrumentpaneler från andra resurser, till exempel en virtuell dator eller ett lagringskonto.

> [!TIP]
> Enskilda paneler på instrumentpanelen tillämpar sina egna åtkomstkontrollkrav baserat på de resurser de visar. Du kan dela en instrumentpanel brett samtidigt som du skyddar data på enskilda paneler.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Förstå åtkomstkontroll för instrumentpaneler

Med rollbaserad åtkomstkontroll (RBAC) kan du tilldela användare till roller på tre olika scopenivåer:

* prenumeration
* Resursgrupp
* resource

De behörigheter som du tilldelar ärver från prenumerationen ner till resursen. Den publicerade instrumentpanelen är en resurs. Du kanske redan har tilldelat användare till roller för prenumerationen som gäller för den publicerade instrumentpanelen.

Anta att du har en Azure-prenumeration och att olika medlemmar i teamet har tilldelats rollerna *som ägare,* *deltagare*eller *läsare* för prenumerationen. Användare som är ägare eller deltagare kan lista, visa, skapa, ändra eller ta bort instrumentpaneler i prenumerationen. Användare som är läsare kan lista och visa instrumentpaneler, men kan inte ändra eller ta bort dem. Användare med läsaråtkomst kan göra lokala ändringar på en publicerad instrumentpanel, till exempel när de felsöker ett problem, men de kan inte publicera ändringarna tillbaka till servern. De kan göra en privat kopia av instrumentpanelen för sig själva.

Du kan också tilldela behörigheter till resursgruppen som innehåller flera instrumentpaneler eller till en enskild instrumentpanel. Du kan till exempel bestämma att en grupp användare ska ha begränsad behörighet i prenumerationen men större åtkomst till en viss instrumentpanel. Tilldela dessa användare till en roll för instrumentpanelen.

## <a name="publish-dashboard"></a>Publicera instrumentpanel

Anta att du konfigurerar en instrumentpanel som du vill dela med en grupp användare i din prenumeration. Följande steg visar hur du delar en instrumentpanel till en grupp som heter Storage Managers. Du kan namnge din grupp vad du vill. Mer information finns [i Hantera grupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Innan du tilldelar åtkomst måste du publicera instrumentpanelen.

1. Välj **Dela**på instrumentpanelen .

    ![välj resurs för instrumentpanelen](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. Välj **Publicera**i **Delning + åtkomstkontroll**.

    ![publicera instrumentpanelen](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Som standard publicerar delning instrumentpanelen till en resursgrupp med namnet **instrumentpaneler**. Om du vill markera en annan resursgrupp avmarkerar du kryssrutan.

Instrumentpanelen har nu publicerats. Om de behörigheter som ärvs från prenumerationen är lämpliga behöver du inte göra något mer. Andra användare i organisationen kan komma åt och ändra instrumentpanelen baserat på deras roll på prenumerationsnivå.

## <a name="assign-access-to-a-dashboard"></a>Tilldela åtkomst till en instrumentpanel

Du kan tilldela en grupp användare till en roll för instrumentpanelen.

1. När du har publicerat instrumentpanelen väljer du alternativet **Dela** eller **Ta bort del** för att komma åt **delning + åtkomstkontroll**.

1. I **Delning + åtkomstkontroll**väljer du **Hantera användare**.

    ![hantera användare för en instrumentpanel](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Välj **Rolltilldelningar** om du vill visa befintliga användare som redan har tilldelats en roll för den här instrumentpanelen.

1. Om du vill lägga till en ny användare eller grupp väljer du **Lägg till** och sedan Lägg **till rolltilldelning**.

    ![lägga till en användare för åtkomst till instrumentpanelen](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Välj den roll som representerar behörigheterna att bevilja. I det här exemplet väljer du **Deltagare**.

1. Välj den användare eller grupp som rollen ska tilldelas. Om du inte ser den användare eller grupp du letar efter i listan använder du sökrutan. Listan över tillgängliga grupper beror på vilka grupper du har skapat i Active Directory.

1. När du har lagt till användare eller grupper väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

* En lista över roller finns [i Inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md).
* Mer information om hur du hanterar resurser finns i [Hantera Azure-resurser med hjälp av Azure-portalen](resource-group-portal.md).

