---
title: "Hur du utför en åtkomst-granskning | Microsoft Docs"
description: "När du har startat en åtkomst-granskning i Azure AD Privileged Identity Management lär du dig hur du slutför den och visa resultaten"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: ca2a1c7c287e4cf6b1b50cfb0068861b6f525596
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Hur du utför en åtkomst-granskning i Azure AD Privileged Identity Management
Privilegierade rollen administratörer kan granska privilegierad åtkomst när en [säkerhetsgranskning har startats](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure AD Privileged Identity Management (PIM) skickar automatiskt ett e-postmeddelande som uppmanar användare att granska deras åtkomst. Om en användare inte fick ett e-postmeddelande, kan du skicka dem instruktionerna [så här utför du en säkerhetsgranskning](active-directory-privileged-identity-management-how-to-perform-security-review.md).

När omprövningsperioden säkerhet är över, eller alla användare har slutfört sin egen granska, följer du stegen i den här artikeln för att hantera granskningen och se resultaten.

## <a name="manage-security-reviews"></a>Hantera säkerhet granskningar
1. Gå till den [Azure-portalen](https://portal.azure.com/) och välj den **Azure AD Privileged Identity Management** på instrumentpanelen.
2. Välj den **åtkomst till granskningar** på instrumentpanelen.
3. Välj den granskning av åtkomst som du vill hantera.

På bladet för åtkomst-granska detalj finns ett antal alternativ för att hantera granskningen.

![PIM-knappar för granskning av åtkomst – skärmbild][1]

### <a name="remind"></a>Påminn
Om en åtkomst-granskning har konfigurerats så att användarna granska själva den **Påminn** knappen skickas ett meddelande. 

### <a name="stop"></a>Stoppa
Alla åtkomst omdömen har ett slutdatum, men du kan använda den **stoppa** för att slutföra den tidigt. Om användare inte har granskats vid den tidpunkten, kommer inte de att kunna när du stoppar granskningen. Du kan inte starta om ett omdöme när den stoppas.

### <a name="apply"></a>Ansök
När en åtkomst-granskning har slutförts, antingen eftersom du har nått slutdatum eller hindrade den manuellt på **tillämpa** knappen implementerar resultatet av granskningen. Om en användares åtkomst nekades i granskningen, är det steg som tar bort sin rolltilldelning.  

### <a name="export"></a>Exportera
Om du vill använda resultatet av säkerhetsgranskning manuellt, kan du exportera granskningen. Den **exportera** knappen startar hämtningen av en CSV-fil. Du kan hantera resultaten i Excel eller andra program som öppnar CSV-filer.

### <a name="delete"></a>Ta bort
Om du inte är intresserad av att granska ytterligare kan du ta bort den. Den **ta bort** knappen tar du bort granskningen av PIM-programmet.

> [!IMPORTANT]
> Du kommer inte får en varning innan borttagningen sker, så att du vill ta bort granskningen. 

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
