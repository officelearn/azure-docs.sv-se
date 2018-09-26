---
title: Dela instrumentpaneler med Azure portal genom att använda RBAC | Microsoft Docs
description: Den här artikeln förklarar hur du delar en instrumentpanel i Azure-portalen med hjälp av rollbaserad åtkomstkontroll.
services: azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: c07a9d92cac13d6325e66f44426f1a64e8ac53cb
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096208"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll
När du har konfigurerat en instrumentpanel, kan du publicera den och dela den med andra användare i din organisation. Du tillåter andra att visa instrumentpanelen med hjälp av Azure [Role-Based Access Control](../role-based-access-control/role-assignments-portal.md). Du tilldelar en användare eller grupp av användare till en roll och rollen definierar om dessa användare kan visa eller ändra publicerade instrumentpanelen. 

Alla publicerade instrumentpaneler implementeras som Azure-resurser, vilket innebär att de finns som hanterbara objekt i din prenumeration och finns i en resursgrupp.  Instrumentpaneler är inte skiljer sig från andra resurser, till exempel en virtuell dator eller ett lagringskonto från en access control perspektiv.

> [!TIP]
> Enskilda paneler på instrumentpanelen tillämpa sina egna krav på åtkomstkontroll utifrån de resurser som de visas.  Därför kan du utforma en instrumentpanel delas brett samtidigt som du skyddar data på enskilda paneler fortfarande.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Förstå åtkomstkontroll för instrumentpaneler
Med rollbaserad åtkomstkontroll (RBAC), kan du tilldela användare till roller på tre olika omfång:

* prenumeration
* Resursgrupp
* resurs

De behörigheter som du tilldelar ärvs från prenumerationen till resursen. Publicerade instrumentpanelen är en resurs. Därför måste kanske du redan användare som tilldelas till roller för prenumerationen som också fungerar för publicerade instrumentpanelen. 

Här är ett exempel.  Anta att du har en Azure-prenumeration och olika medlemmarna i gruppen har tilldelats rollerna för **ägare**, **deltagare**, eller **läsare** för prenumerationen. Användare som är ägare eller deltagare kan visa, visa, skapa, ändra eller ta bort instrumentpaneler i prenumerationen.  Användare som är läsare kan inte kan listan och visa instrumentpaneler, men ändra eller ta bort dem.  Användare med läsåtkomst ska kunna göra lokala ändringar i en publicerad instrumentpanel (t.ex, när du felsöker ett problem), men det går inte att publicera ändringarna på servern.  De kommer att ha möjlighet att göra en privat kopia av instrumentpanelen för själva

Men kan du också tilldela behörigheter till den resursgrupp som innehåller flera instrumentpaneler eller till en enskild instrumentpanel. Du kan till exempel bestämma att en grupp användare bör ha begränsad behörighet över prenumeration men större åtkomst till en viss instrumentpanel. Du kan tilldela dessa användare till en roll för instrumentpanelen. 

## <a name="publish-dashboard"></a>Publicera instrumentpanel
Anta att du har konfigurerat en instrumentpanel som du vill dela med en grupp av användare i din prenumeration. Stegen nedan visas en anpassad grupp som heter Storage Manager, men du kan namnge din grupp vad du vill ha. Information om att skapa en Active Directory-grupp och lägga till användare i gruppen finns i [hantera grupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. I instrumentpanelen, väljer **resursen**.
   
     ![Välj resurs](./media/azure-portal-dashboard-share-access/select-share.png)
2. Innan du tilldelar åtkomst, måste du publicera instrumentpanelen. Som standard instrumentpanelen kommer att publiceras i en resursgrupp med namnet **instrumentpaneler**. Välj **Publicera**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Instrumentpanelen har nu publicerats. Du behöver inte göra någonting mer om de behörigheter som ärvs från prenumerationen är lämpliga. Andra användare i din organisation kommer att kunna komma åt och ändra en instrumentpanel baserat på deras prenumeration på roll. Men i den här självstudien ska vi tilldela en grupp av användare till en roll för instrumentpanelen.

## <a name="assign-access-to-a-dashboard"></a>Tilldela åtkomst till en instrumentpanel
1. När du har publicerat instrumentpanelen, Välj **hantera användare**.
   
     ![hantera användare](./media/azure-portal-dashboard-share-access/manage-users.png)
2. En lista över befintliga användare som redan har tilldelats en roll för den här instrumentpanelen visas. Listan över befintliga användare ska skilja sig från bilden nedan. Mest sannolikt ärvs tilldelningarna från prenumerationen. Om du vill lägga till en ny användare eller grupp, Välj **Lägg till**.
   
     ![Lägg till användare](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Välj den roll som representerar de behörigheter som du vill bevilja. Det här exemplet väljer **deltagare**.
   
     ![Välj roll](./media/azure-portal-dashboard-share-access/select-role.png)
4. Välj användare eller grupp som du vill tilldela rollen. Använd sökrutan Om du inte ser den användare eller grupp som du söker efter i listan. Listan över tillgängliga grupper beror på de grupper som du har skapat i din Active Directory.
   
     ![Välj användare](./media/azure-portal-dashboard-share-access/select-user.png) 
5. När du har lagt till användare eller grupper, Välj **OK**. 
6. Den nya tilldelningen har lagts till i listan över användare. Observera att dess **åtkomst** anges som **tilldelad** snarare än **ärvd**.
   
     ![tilldelade roller](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Nästa steg
* En lista över roller finns i [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md).
* Läs om hur du hanterar resurser i [hantera Azure-resurser via portalen](resource-group-portal.md).

