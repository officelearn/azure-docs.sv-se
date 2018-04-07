---
title: Privileged Identity Management resurser för Azure - fullständig åtkomst granska för Azure-resurser | Microsoft Docs
description: Beskriver hur du utför en åtkomst-granskning för Azure-resurser.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management - resurs role - Slutför åtkomst granskning
Privilegierade rollen administratörer kan granska privilegierad åtkomst när en [säkerhetsgranskning har startats](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) för Azure-resurser skickar automatiskt ett e-postmeddelande som uppmanar användare att granska deras åtkomst. Om en användare inte fick ett e-postmeddelande, kan du skicka dem instruktionerna [så här utför du en säkerhetsgranskning](pim-resource-roles-perform-access-review.md).

När omprövningsperioden säkerhet är över, eller alla användare har slutfört sin egen granska, följer du stegen i den här artikeln för att hantera granskningen och se resultaten.

## <a name="manage-security-reviews"></a>Hantera säkerhet granskningar
1. Gå till den [Azure-portalen](https://portal.azure.com/) och välj den **Azure-resurser** på instrumentpanelen.
2. Välj din resurs.
3. Välj den **åtkomst till granskningar** på instrumentpanelen.
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. Välj den granskning av åtkomst som du vill hantera.

På bladet för åtkomst-granska detalj finns ett antal alternativ för att hantera granskningen.
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stoppa
Alla åtkomst omdömen har ett slutdatum, men du kan använda den **stoppa** för att slutföra den tidigt. Om användare inte har granskats vid den tidpunkten, kommer inte de att kunna när du stoppar granskningen. Du kan inte starta om ett omdöme när den stoppas.

### <a name="reset"></a>Återställ
Du kan återställa en åtkomst-granskning för att ta bort alla beslut som görs på den. När du har återställer en åtkomst-granskning, markeras alla användare som inte granskats igen. 

### <a name="apply"></a>Använd
När en åtkomst-granskning har slutförts, antingen eftersom du har nått slutdatum eller hindrade den manuellt på **tillämpa** knappen implementerar resultatet av granskningen. Om en användares åtkomst nekades i granskningen, är det steg som tar bort sin rolltilldelning.  

### <a name="delete"></a>Ta bort
Om du inte är intresserad av att granska ytterligare kan du ta bort den. Den **ta bort** knappen tar du bort granskningen av PIM-programmet.

## <a name="results"></a>Resultat
Visa och hämta en lista över granska resultaten på fliken resultat. ![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Granskare
Visa och lägga till granskare i din befintliga åtkomst granskning. Påminn granskare att slutföra granskningarna.
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



