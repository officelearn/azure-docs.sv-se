---
title: Slutför en åtkomst granskning av Azure Resource roles i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du utför en åtkomst granskning av Azure Resource roles i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b9563a4032011b999bf867fc782ba4cbb9c3fac
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804254"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Slutför en åtkomst granskning av Azure Resource roles i PIM
Privilegierade roll administratörer kan granska privilegie rad åtkomst när en [åtkomst granskning har startats](pim-resource-roles-start-access-review.md). Azure Active Directory (Azure AD) Privileged Identity Management (PIM) skickar automatiskt ett e-postmeddelande som kräver att användarna granskar sin åtkomst. Om en användare inte får ett e-postmeddelande kan du skicka dem till instruktioner för [hur du utför en åtkomst granskning](pim-resource-roles-perform-access-review.md).

När åtkomst gransknings perioden är över, eller när alla användare har avslutat sin egen granskning, följer du stegen i den här artikeln för att hantera granskningen och se resultatet.

## <a name="manage-access-reviews"></a>Hantera åtkomst granskningar
1. Gå till [Azure-portalen](https://portal.azure.com/). På instrument panelen väljer du sedan programmet **Azure-resurser** .

2. Välj din resurs.

3. Välj avsnittet **åtkomst granskningar** på instrument panelen.

    ![Azure-resurser – åtkomst gransknings listan visar roll, ägare, start datum, slutdatum och status](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Välj den åtkomst granskning som du vill hantera.

På bladet detaljer i åtkomst granskningen finns ett antal alternativ för att hantera den här granskningen. Alternativen är följande:

![Alternativ för att hantera en granskning-stopp, återställning, tillämpa och ta bort](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stoppa
Alla åtkomst granskningar har ett slutdatum, men du kan använda **stopp** -knappen för att avsluta den tidigt. Alla användare som inte har slutfört sin granskning vid den här tiden kommer inte att kunna slutföra den när du har stoppat granskningen. Du kan inte starta om en granskning när den har stoppats.

### <a name="reset"></a>Återställ
Du kan återställa en åtkomst granskning för att ta bort alla beslut som fattas på den. När du har återställt en åtkomst granskning markeras alla användare som avvisade igen. 

### <a name="apply"></a>Använd
När en åtkomst granskning har slutförts använder du knappen **tillämpa** för att implementera resultatet av granskningen. Om en användares åtkomst nekades i granskningen tar det här steget bort roll tilldelningen.  

### <a name="delete"></a>Ta bort
Om du inte är intresse rad av att granska mer tar du bort den. Med knappen **ta bort** tas granskningen bort från PIM-programmet.

## <a name="results"></a>Resultat
På sidan **resultat** kan du Visa och hämta en lista över dina gransknings resultat. 

![Resultat sida som visar användare, resultat, orsak, granskning av, tillämpad av och tillämpa resultat](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Granskare
Visa och Lägg till granskare i din befintliga åtkomst granskning. Påminn granskare för att slutföra granskningarna.

![Sid lista för granskare, namn och User Principal Name](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomstgranskning för Azure-resursroller i PIM](pim-resource-roles-start-access-review.md)
- [Utför en åtkomstgranskning av mina Azure-resursroller i PIM](pim-resource-roles-perform-access-review.md)
