---
title: Slutför en åtkomst-granskning för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du utför en åtkomst-granskning för Azure-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 1dd5eb4a2a0a4edd15e5299754028570806db09b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699462"
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Slutför en åtkomst-granskning för Azure-resurser med hjälp av Privileged Identity Management
Privilegierade rollen administratörer kan granska privilegierad åtkomst efter en [säkerhetsgranskning har startats](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) för Azure-resurser skickar automatiskt ett e-postmeddelande som uppmanar användaren att granska deras åtkomst. Om en användare inte har fått ett e-postmeddelande, kan du skicka dem instruktionerna [så här utför du en säkerhetsgranskning](pim-resource-roles-perform-access-review.md).

När omprövningsperioden säkerhet är över, eller när alla användare har slutfört sin egen granska, följer du stegen i den här artikeln för att hantera granskningen och se resultaten.

## <a name="manage-security-reviews"></a>Hantera säkerhet granskningar
1. Gå till [Azure-portalen](https://portal.azure.com/). På instrumentpanelen, väljer den **Azure-resurser** program.

2. Välj din resurs.

3. Välj den **åtkomst till granskningar** på instrumentpanelen.
![Åtkomstgranskningar](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Välj den granskning av åtkomst som du vill hantera.

Det finns ett antal alternativ för att hantera granskningen på bladet detaljer för granska åtkomst. Alternativen är följande:

![Alternativ för att hantera en granskning](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stoppa
Alla åtkomst omdömen har ett slutdatum, men du kan använda den **stoppa** för att slutföra den tidigt. Alla användare som inte är slutförda granskning vid den tidpunkten kommer inte att slutföra den när du stoppar granskningen. Du kan inte starta om ett omdöme när den stoppas.

### <a name="reset"></a>Återställ
Du kan återställa en åtkomst-granskning för att ta bort alla beslut som görs på den. När du har återställt en åtkomst-granskning, markeras alla användare som inte granskats igen. 

### <a name="apply"></a>Använd
När en åtkomst-granskning är klar, den **tillämpa** för att implementera resultatet av granskningen. Om en användares åtkomst nekades i granskningen det här steget tar bort sin rolltilldelning.  

### <a name="delete"></a>Ta bort
Om du inte är intresserad i granskningen längre kan du ta bort den. Den **ta bort** knappen tar du bort granskningen av PIM-programmet.

## <a name="results"></a>Resultat
På den **resultat** fliken, visa och hämta en lista över granska resultaten. 
![Fliken resultat](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Granskare
Visa och lägga till granskare i din befintliga åtkomst granskning. Påminn granskare att slutföra granskningarna.
![Lägga till granskare](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



