---
title: Så här tar du bort en användares åtkomst till ett program i Azure Active Directory
description: Förstå hur du tar bort en användares åtkomst till ett program i Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: 8544c35d77dfc32ece9b21a602f301ab15c22439
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288324"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Så här tar du bort en användares åtkomst till ett program

Den här artikeln hjälper dig att förstå hur du tar bort en användares åtkomst till ett program.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort tilldelningen för en specifik användare eller grupp till ett program

Om du vill ta bort en användare eller grupp tilldelning till ett program följer du stegen i artikeln [ta bort en användare eller grupp tilldelning från en företags app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artikeln.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användar inloggningar till ett program följer du stegen i avsnittet [Inaktivera användar inloggningar för en Enterprise-App i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artikeln.

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

[Snabb starts serien i program hantering](delete-application-portal.md) innehåller vägledning om hur du tar bort ett program från Azure Active Directory-klienten.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida åtgärder för användar medgivande till alla program

Om du inaktiverar användar medgivande för hela katalogen kan slutanvändarna inte godkänna något program. Administratörer kan fortfarande godkänna användarens räkning. Om du vill ha mer information om program medgivande och varför du kanske inte vill göra detta, Läs [förstå användar-och administratörs medgivande](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Se även [behörigheter och medgivande](../develop/v2-permissions-and-consent.md).

Följ dessa instruktioner om du vill **inaktivera alla framtida användar medgivande åtgärder i hela katalogen** :

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** 

3.  Klicka på **företags program** på navigerings menyn.

5.  Klicka på **användar inställningar**.

6.  Ange att **användarna ska kunna tillåta att appar får åtkomst till företags data för deras räkning** , växla till **Nej** och klicka på knappen Spara.


## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till appar](what-is-access-management.md)
