---
title: Hur du tar bort en användares åtkomst till ett program | Microsoft Docs
description: Förstå hur du tar bort en användares åtkomst till ett program
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 0deb5215c1379ac552a492f4b9e90df83201aebf
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364489"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Hur du tar bort en användares åtkomst till ett program

Den här artikeln hjälper dig att förstå hur du tar bort en användares åtkomst till ett program.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort en specifik användare eller grupp-tilldelning till ett program

Ta bort en användare eller grupptilldelning till ett program genom att följa instruktionerna i den [ta bort en användare eller grupp från en företagsapp i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artikeln.

. ## jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användarinloggningar till ett program följer du stegen i den [inaktivera användarinloggningar för en företagsapp i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artikeln.

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

Att **ta bort ett program**, följer du dessa instruktioner:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill ta bort.

7.  När programmet har lästs in klickar du på **ta bort** ikonen från övre programmets **översikt** fönstret.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida medgivande användaråtgärder för program

Inaktiverar användarens medgivande för hela katalogen förhindra slutanvändare från principer för alla program. Administratörer kan fortfarande ge samtycke på användarens behalves. Mer information om programtillåtelser och varför du kanske eller kanske inte vill göra detta, Läs [Förstå användar- och administratörens godkännande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Att **inaktivera alla framtida medgivande användaråtgärder i hela katalogen**, följer du dessa instruktioner:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **användarinställningar**.

6.  Inaktivera alla framtida medgivande användaråtgärder genom att ange den **användare kan bevilja appar åtkomst till sina data** växla till **nr** och klicka på den **spara** knappen.


# <a name="next-steps"></a>Nästa steg
[Hantera åtkomst till appar](manage-apps/what-is-access-management.md)
