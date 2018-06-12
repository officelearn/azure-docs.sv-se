---
title: Ta bort en användare åtkomst till ett program | Microsoft Docs
description: Förstå hur du tar bort en användare åtkomst till ett program
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3731049d2bc42c7fac492f7609f54b5d4a1443b6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292328"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Ta bort en användare åtkomst till ett program

Den här artikeln hjälper dig att förstå hur du tar bort en användare åtkomst till ett program.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort en specifik användare eller grupp tilldelning till ett program

Ta bort en användare eller grupptilldelning till ett program genom att följa instruktionerna i den [ta bort en användare eller grupp från en enterprise-app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artikel.

. ## jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användarinloggningar till ett program följer du stegen i den [inaktivera användarinloggningar för en enterprise-app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artikel.

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

Att **ta bort ett program**, följer du dessa anvisningar:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill ta bort.

7.  När programmet läses in klickar du på **ta bort** ikon från översta programmet **översikt** fönstret.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida användarens medgivande åtgärder för alla program

Inaktiverar användargodkännande för hela katalogen att förhindra att slutanvändare principer för alla program. Administratörer kan fortfarande medgivande på användarens behalves. Lär dig mer om programmet medgivande och varför du kanske eller kanske inte vill göra detta, Läs [förstå användare och admin medgivande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Att **inaktivera alla framtida användarens medgivande åtgärder i hela katalogen**, följer du dessa anvisningar:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **användarinställningar**.

6.  Inaktivera alla framtida användarens medgivande åtgärder genom att ange den **användare kan Tillåt appar att komma åt sina data** växla till **nr** och klicka på den **spara** knappen.


# <a name="next-steps"></a>Nästa steg
[Hantera åtkomst till appar](manage-apps/what-is-access-management.md)
