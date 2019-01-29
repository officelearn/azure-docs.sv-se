---
title: Hur du tar bort en användares åtkomst till ett program | Microsoft Docs
description: Förstå hur du tar bort en användares åtkomst till ett program
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: barbkess
ms.openlocfilehash: 00f0df4612fe523b8b585bc2c9458386422256e8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180508"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Hur du tar bort en användares åtkomst till ett program

Den här artikeln hjälper dig att förstå hur du tar bort en användares åtkomst till ett program.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort en specifik användare eller grupp-tilldelning till ett program

Ta bort en användare eller grupptilldelning till ett program genom att följa instruktionerna i den [ta bort en användare eller grupp från en företagsapp i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artikeln.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

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

Inaktiverar användarens medgivande för hela katalogen förhindra slutanvändare från principer för alla program. Administratörer kan fortfarande ge samtycke på användarens behalves. Mer information om programmet samtycke och varför du kanske eller kanske inte vill göra detta, Läs [Förstå användar- och administratörens godkännande](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Se även [behörigheter och samtycke](../develop/v2-permissions-and-consent.md).

Att **inaktivera alla framtida medgivande användaråtgärder i hela katalogen**, följer du dessa instruktioner:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** 

3.  Klicka på **företagsprogram** på navigeringsmenyn.

5.  Klicka på **användarinställningar**.

6.  Ange den **användare kan bevilja appar åtkomst till företagets data å deras vägnar** växla till **nr** och klicka på knappen Spara.


## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till appar](what-is-access-management.md)
