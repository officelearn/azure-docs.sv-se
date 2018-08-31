---
title: Slutför en åtkomstgranskning för Azure-resursroller i PIM | Microsoft Docs
description: Lär dig hur du slutför en åtkomstgranskning för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f998c509e9bea65980367690a5e9d03f579b8e98
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189135"
---
# <a name="complete-an-access-review-for-azure-resource-roles-in-pim"></a>Slutför en åtkomstgranskning för Azure-resursroller i PIM
Privilegierade rolladministratörer kan granska privilegierad åtkomst efter en [åtkomstgranskning har startats](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) för Azure-resurser skickar automatiskt ett e-postmeddelande som uppmanar användaren att granska åtkomsten. Om en användare inte får ett e-postmeddelande, kan du skicka dem instruktionerna [så här utför du en åtkomstgranskning](pim-resource-roles-perform-access-review.md).

När åtkomstgranskningsperiod är slut, eller när alla användare är klar med sin egen granska, följer du stegen i den här artikeln om du vill hantera granskningen och se resultaten.

## <a name="manage-access-reviews"></a>Hantera åtkomstgranskningar
1. Gå till [Azure-portalen](https://portal.azure.com/). På instrumentpanelen, Välj den **Azure-resurser** program.

2. Välj din resurs.

3. Välj den **Åtkomstgranskningar** på instrumentpanelen.
![Åtkomstgranskningar](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Välj åtkomstgranskning som du vill hantera.

På bladet detalj i åtkomstgranskningen finns ett antal alternativ för att hantera granskningen. Alternativen är följande:

![Alternativ för att hantera en granskning](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stoppa
Alla åtkomstgranskningar har ett slutdatum, men du kan använda den **stoppa** för att slutföra den tidigt. Alla användare som inte har slutfört sina granskningar av den här tiden kan inte slutför du det när du stoppa granskningen. Du kan inte starta om en granskning när den har stoppats.

### <a name="reset"></a>Återställ
Du kan återställa en åtkomstgranskning för att ta bort alla beslut som görs på den. När du har återställt en åtkomstgranskning, alla användare är markerade som ej granskade igen. 

### <a name="apply"></a>Använd
När en åtkomstgranskning är klar kan du använda den **tillämpa** knappen för att implementera resultatet av granskningen. Om en användares åtkomst nekades i granskningen, det här steget tar du bort deras rolltilldelning.  

### <a name="delete"></a>Ta bort
Om du inte är intresserad av granskningen längre, kan du ta bort den. Den **ta bort** knappen tar bort granskningen från PIM-programmet.

## <a name="results"></a>Resultat
På den **resultat** fliken, visa och hämta en lista över granskningsresultaten. 
![Fliken resultat](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Granskare
Visa och Lägg till granskare till din befintliga åtkomstgranskning. Påminn granskare att slutföra sina granskningar.
![Lägg till granskare](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomstgranskning för Azure-resursroller i PIM](pim-resource-roles-start-access-review.md)
- [Utför en åtkomstgranskning av min Azure-resursroller i PIM](pim-resource-roles-perform-access-review.md)
