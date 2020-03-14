---
title: Dela Azure Portal-instrumentpaneler med hjälp av rollbaserad Access Control
description: Den här artikeln förklarar hur du delar en instrument panel i Azure Portal med hjälp av rollbaserad Access Control.
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
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: e8d251cef9e67cb8fc0c11df8ce546383f75a679
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248468"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Dela Azure-instrumentpaneler med hjälp av rollbaserad Access Control

När du har konfigurerat en instrument panel kan du publicera den och dela den med andra användare i din organisation. Du tillåter andra att visa instrument panelen med hjälp av RBAC (Azure [rollbaserad Access Control](../role-based-access-control/role-assignments-portal.md) ). Tilldela en användare eller grupp av användare till en roll. Rollen definierar om dessa användare kan visa eller ändra den publicerade instrument panelen.

Alla publicerade instrument paneler implementeras som Azure-resurser. De finns som hanterbara objekt i din prenumeration och ingår i en resurs grupp. Från ett perspektiv för åtkomst kontroll skiljer sig instrument paneler inte från andra resurser, till exempel en virtuell dator eller ett lagrings konto.

> [!TIP]
> Enskilda paneler på instrument panelen tillämpar sina egna krav på åtkomst kontroll baserat på de resurser som de visar. Du kan dela en instrument panel i stort sett samtidigt som du skyddar data i enskilda paneler.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Förstå åtkomst kontroll för instrument paneler

Med rollbaserad Access Control (RBAC) kan du tilldela användare till roller på tre olika nivåer:

* prenumeration
* Resursgrupp
* resource

Behörigheterna som du tilldelar ärver från prenumerationen till resursen. Den publicerade instrument panelen är en resurs. Du kanske redan har tilldelat användare roller för den prenumeration som gäller för den publicerade instrument panelen.

Anta att du har en Azure-prenumeration och att olika medlemmar i teamet har tilldelats rollen som *ägare*, *deltagare*eller *läsare* för prenumerationen. Användare som är ägare eller deltagare kan visa, Visa, skapa, ändra eller ta bort instrument paneler i prenumerationen. Användare som är läsare kan visa och Visa instrument paneler, men kan inte ändra eller ta bort dem. Användare med läsar åtkomst kan göra lokala redigeringar till en publicerad instrument panel, till exempel vid fel sökning av problem, men de kan inte publicera tillbaka ändringarna till servern. De kan skapa en privat kopia av instrument panelen för sig själva.

Du kan också tilldela behörigheter till resurs gruppen som innehåller flera instrument paneler eller till en enskild instrument panel. Du kan till exempel bestämma att en grupp användare ska ha begränsade behörigheter i prenumerationen, men större åtkomst till en viss instrument panel. Tilldela dessa användare till en roll för instrument panelen.

## <a name="publish-dashboard"></a>Publicera instrumentpanel

Vi antar att du konfigurerar en instrument panel som du vill dela med en grupp användare i din prenumeration. Följande steg visar hur du delar en instrument panel i en grupp som kallas lagrings hanterare. Du kan namnge din grupp oavsett vad du vill. Mer information finns i [hantera grupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Innan du tilldelar åtkomst måste du publicera instrument panelen.

1. I instrument panelen väljer du **dela**.

    ![Välj resurs för din instrument panel](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. I **delning + åtkomst kontroll**väljer du **publicera**.

    ![publicera din instrument panel](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Som standard publicerar delning din instrument panel till en resurs grupp med namnet **instrument paneler**.

Din instrument panel har nu publicerats. Om de behörigheter som ärvts från prenumerationen är lämpliga behöver du inte göra något mer. Andra användare i din organisation kan komma åt och ändra instrument panelen baserat på deras prenumerations nivå roll.

## <a name="assign-access-to-a-dashboard"></a>Tilldela åtkomst till en instrument panel

Du kan tilldela en grupp användare en roll för den instrument panelen.

1. När du har publicerat instrument panelen går du till **delning + åtkomst kontroll**och väljer **Hantera användare**.

    ![hantera användare för en instrument panel](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

    Om du vill ha åtkomst till **delning + åtkomst kontroll** från en instrument panel väljer du alternativet **dela** eller ta **bort delning** .

1. Välj **roll tilldelningar** för att se befintliga användare som redan har tilldelats en roll för den här instrument panelen.

1. Välj **Lägg**till om du vill lägga till en ny användare eller grupp.

    ![Lägg till en användare för åtkomst till instrument panelen](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Välj den roll som representerar behörigheterna som ska beviljas. I det här exemplet väljer du **deltagare**.

1. Välj den användare eller grupp som du vill tilldela rollen. Om du inte ser den användare eller grupp som du letar efter i listan använder du sökrutan. Listan över tillgängliga grupper beror på de grupper som du har skapat i Active Directory.

1. När du har lagt till användare eller grupper väljer du **OK**.

    Den nya tilldelningen läggs till i listan över användare. **Åtkomsten** anges som **tilldelad** snarare än **ärvd**.

    ![tilldelade roller](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Nästa steg

* En lista över roller finns i [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md).
* Mer information om hur du hanterar resurser finns i [Hantera Azure-resurser med hjälp av Azure Portal](resource-group-portal.md).

