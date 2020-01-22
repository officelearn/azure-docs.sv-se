---
title: Dela Azure Portal instrument paneler med RBAC | Microsoft Docs
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
ms.date: 08/01/2016
ms.author: mblythe
ms.openlocfilehash: ce94a35ebcbf5d8cf3d176f05ac75ea5da5d8d99
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310755"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll

När du har konfigurerat en instrument panel kan du publicera den och dela den med andra användare i din organisation. Du låter andra Visa din instrument panel med hjälp av Azures [rollbaserade Access Control](../role-based-access-control/role-assignments-portal.md). Du tilldelar en användare eller grupp användare till en roll, och rollen definierar om dessa användare kan visa eller ändra den publicerade instrument panelen. 

Alla publicerade instrument paneler implementeras som Azure-resurser, vilket innebär att de finns som hanterbara objekt i din prenumeration och finns i en resurs grupp.  Från ett perspektiv för åtkomst kontroll skiljer sig instrument paneler inte från andra resurser, till exempel en virtuell dator eller ett lagrings konto.

> [!TIP]
> Enskilda paneler på instrument panelen tillämpar sina egna krav på åtkomst kontroll baserat på de resurser som de visar.  Därför kan du skapa en instrument panel som delas på ett stort sätt samtidigt som du fortfarande skyddar data i enskilda paneler.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Förstå åtkomst kontroll för instrument paneler
Med rollbaserad Access Control (RBAC) kan du tilldela användare till roller på tre olika nivåer:

* prenumeration
* Resursgrupp
* resource

Behörigheterna som du tilldelar ärvs från prenumerationen till resursen. Den publicerade instrument panelen är en resurs. Därför kanske du redan har tilldelat användare roller för prenumerationen som också fungerar för den publicerade instrument panelen. 

Här är ett exempel.  Anta att du har en Azure-prenumeration och att olika medlemmar i teamet har tilldelats rollen som **ägare**, **deltagare**eller **läsare** för prenumerationen. Användare som är ägare eller deltagare kan visa, Visa, skapa, ändra eller ta bort instrument paneler i prenumerationen.  Användare som är läsare kan visa och Visa instrument paneler, men kan inte ändra eller ta bort dem.  Användare med läsar åtkomst kan göra lokala redigeringar till en publicerad instrument panel (till exempel vid fel sökning av ett problem), men de kan inte publicera tillbaka ändringarna till servern.  De kommer att ha möjlighet att skapa en privat kopia av instrument panelen för sig själva

Du kan dock också tilldela behörigheter till resurs gruppen som innehåller flera instrument paneler eller till en enskild instrument panel. Du kan till exempel bestämma att en grupp användare ska ha begränsade behörigheter i prenumerationen, men större åtkomst till en viss instrument panel. Du tilldelar dessa användare till en roll för instrument panelen. 

## <a name="publish-dashboard"></a>Publicera instrumentpanel
Låt oss anta att du har konfigurerat en instrument panel som du vill dela med en grupp användare i din prenumeration. Stegen nedan visar en anpassad grupp som kallas lagrings hanterare, men du kan ge din grupp det du vill. Information om hur du skapar en Active Directory grupp och lägger till användare i gruppen finns i [hantera grupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. I instrument panelen väljer du **dela**.
   
     ![Välj resurs](./media/azure-portal-dashboard-share-access/select-share.png)
2. Innan du tilldelar åtkomst måste du publicera instrument panelen. Som standard kommer instrument panelen att publiceras till en resurs grupp med namnet **instrument paneler**. Välj **Publicera**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Din instrument panel har nu publicerats. Om de behörigheter som ärvts från prenumerationen är lämpliga behöver du inte göra något mer. Andra användare i din organisation kan komma åt och ändra instrument panelen baserat på deras prenumerations nivå roll. I den här självstudien ska vi dock tilldela en grupp användare till en roll för instrument panelen.

## <a name="assign-access-to-a-dashboard"></a>Tilldela åtkomst till en instrument panel
1. När du har publicerat instrument panelen väljer du **Hantera användare**.
   
     ![hantera användare](./media/azure-portal-dashboard-share-access/manage-users.png)
2. En lista över befintliga användare visas som redan har tilldelats en roll för den här instrument panelen. Din lista över befintliga användare kommer att skilja sig från bilden nedan. Förmodligen ärvs tilldelningarna från prenumerationen. Välj **Lägg**till om du vill lägga till en ny användare eller grupp.
   
     ![Lägg till användare](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Välj rollen som representerar de behörigheter som du vill bevilja. I det här exemplet väljer du **deltagare**.
   
     ![Välj roll](./media/azure-portal-dashboard-share-access/select-role.png)
4. Välj den användare eller grupp som du vill tilldela rollen. Om du inte ser den användare eller grupp som du letar efter i listan använder du sökrutan. Listan över tillgängliga grupper beror på de grupper som du har skapat i din Active Directory.
   
     ![Välj användare](./media/azure-portal-dashboard-share-access/select-user.png) 
5. När du har lagt till användare eller grupper väljer du **OK**. 
6. Den nya tilldelningen läggs till i listan över användare. Observera att **åtkomsten** visas som **tilldelad** i stället för **ärvd**.
   
     ![tilldelade roller](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Nästa steg
* En lista över roller finns i [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md).
* Mer information om hur du hanterar resurser finns i [Hantera Azure-resurser via portalen](resource-group-portal.md).

