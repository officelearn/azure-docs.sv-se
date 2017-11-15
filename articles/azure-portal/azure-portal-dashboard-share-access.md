---
title: "Dela instrumentpaneler med Azure portal genom att använda RBAC | Microsoft Docs"
description: "Den här artikeln beskriver hur du delar en instrumentpanel i Azure-portalen med hjälp av rollbaserad åtkomstkontroll."
services: azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: multiple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: 50a965f9773524a36bedc3d040bf1a1ca391507f
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Dela Azure instrumentpaneler med hjälp av rollbaserad åtkomstkontroll
När du har konfigurerat en instrumentpanel kan du publicera den och dela den med andra användare i din organisation. Du att andra ska kunna visa instrumentpanelen med hjälp av Azure [rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md). Du tilldelar en användare eller grupp av användare till en roll och rollen definierar om dessa användare kan visa eller ändra publicerade instrumentpanelen. 

Alla publicerade instrumentpaneler implementeras som Azure-resurser, vilket innebär att de finns som hanterbara objekt inom din prenumeration och ingår i en resursgrupp.  Ur ett access control skiljer instrumentpaneler sig vissa resurser, till exempel en virtuell dator eller ett lagringskonto.

> [!TIP]
> Enskilda paneler på instrumentpanelen tillämpa sina egna krav på åtkomstkontroll baserat på de resurser som de visas.  Därför kan du utforma en instrumentpanel som delas brett samtidigt skyddas data på enskilda paneler.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Förstå behörighet för instrumentpaneler
Med rollbaserad åtkomstkontroll (RBAC), kan du tilldela användare till roller på tre olika nivåer av omfång:

* prenumeration
* Resursgrupp
* resurs

De behörigheter du tilldelar ärvs från prenumerationen till resursen. Publicerade instrumentpanelen är en resurs. Därför kanske redan användare som tilldelats roller för prenumerationen som fungerar även för publicerade instrumentpanelen. 

Här är ett exempel.  Anta att du har en Azure-prenumeration och olika medlemmarna i gruppen har tilldelats rollerna i **ägare**, **deltagare**, eller **reader** för prenumerationen. Användare som är ägare eller deltagare kan visa, visa, skapa, ändra eller ta bort instrumentpaneler i prenumerationen.  Användare som är läsare kan inte kan listan och visa instrumentpaneler, men ändra eller ta bort dem.  Användare med reader åtkomst ska kunna göra lokala ändringar i en publicerad instrumentpanel (t.ex, när du felsöker ett problem), men kan inte publicera ändringarna på servern.  De har du möjlighet att göra en privat kopia av instrumentpanelen för sig själva

Du kan dock också tilldela behörigheter till den resursgrupp som innehåller flera instrumentpaneler eller till en enskild instrumentpanel. Du kan till exempel bestämma att en grupp användare bör ha begränsad behörighet över prenumerationen men större åtkomst till en viss instrumentpanel. Du tilldelar dessa användare till en roll för instrumentpanelen. 

## <a name="publish-dashboard"></a>publicera instrumentpanelen
Anta att du har konfigurerat en instrumentpanel som du vill dela med en grupp användare i din prenumeration. Stegen nedan beskriver en anpassad grupp som heter Storage Manager, men du kan också namnge din grupp vad du vill. Information om hur du skapar en Active Directory-grupp och lägga till användare i gruppen finns [hantera grupper i Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

1. I instrumentpanelen, väljer **resursen**.
   
     ![Välj resurs](./media/azure-portal-dashboard-share-access/select-share.png)
2. Innan du tilldelar åtkomst, måste du publicera instrumentpanelen. Som standard instrumentpanelen kommer att publiceras till en resursgrupp med namnet **instrumentpaneler**. Välj **publicera**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Instrumentpanelen publiceras nu. Om de behörigheter som ärvts från prenumerationen är lämpligt, behöver du inte göra någonting. Andra användare i din organisation kommer att kunna komma åt och ändra instrumentpanelen med syftet för prenumerationen. Men för den här självstudiekursen vi tilldela en grupp användare till en roll för instrumentpanelen.

## <a name="assign-access-to-a-dashboard"></a>Tilldela åtkomst till en instrumentpanel
1. När du har publicerat instrumentpanelen, Välj **hantera användare**.
   
     ![hantera användare](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Du kommer se en lista över befintliga användare som redan har tilldelats en roll för den här instrumentpanelen. I listan över befintliga användare kommer att skiljer sig på bilden nedan. Troligen ärvs tilldelningarna från prenumerationen. Om du vill lägga till en ny användare eller grupp, Välj **Lägg till**.
   
     ![Lägg till användare](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Välj den roll som representerar de behörigheter som du vill bevilja. Det här exemplet väljer du **deltagare**.
   
     ![Välj rollen](./media/azure-portal-dashboard-share-access/select-role.png)
4. Välj användare eller grupp som du vill tilldela rollen. Om du inte ser användaren eller gruppen som du söker efter i listan, kan du använda sökrutan. I listan över tillgängliga grupper beror på de grupper som du har skapat i Active Directory.
   
     ![Välj användare](./media/azure-portal-dashboard-share-access/select-user.png) 
5. När du har lagt till användare eller grupper, Välj **OK**. 
6. Den nya tilldelningen har lagts till i listan över användare. Observera att dess **åtkomst** anges som **tilldelad** snarare än **ärvda**.
   
     ![tilldelade roller](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Nästa steg
* En lista över roller finns [RBAC: inbyggda roller](../active-directory/role-based-access-built-in-roles.md).
* Läs om hur du hanterar resurser i [hantera Azure-resurser via portalen](resource-group-portal.md).

