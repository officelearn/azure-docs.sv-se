---
title: Så här tar du bort en användares åtkomst till ett program | Microsoft Docs
description: Förstå hur du tar bort en användares åtkomst till ett program
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b69502995eff88df53af3671a8e611809f83e59
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "65826101"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Så här tar du bort en användares åtkomst till ett program

Den här artikeln hjälper dig att förstå hur du tar bort en användares åtkomst till ett program.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort tilldelningen för en specifik användare eller grupp till ett program

Om du vill ta bort en användare eller grupp tilldelning till ett program följer du stegen i artikeln [ta bort en användare eller grupp tilldelning från en företags app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artikeln.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användar inloggningar till ett program följer du stegen i avsnittet [Inaktivera användar inloggningar för en Enterprise-App i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artikeln.

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

Om du vill **ta bort ett program**följer du dessa anvisningar:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill ta bort.

7. När programmet har lästs in klickar du på **ta bort** ikon i det översta programmets **översikts** fönster.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida åtgärder för användar medgivande till alla program

Om du inaktiverar användar medgivande för hela katalogen kan slutanvändarna inte godkänna något program. Administratörer kan fortfarande godkänna användarens räkning. Om du vill ha mer information om program medgivande och varför du kanske inte vill göra detta, Läs [förstå användar-och administratörs medgivande](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Se även [behörigheter och medgivande](../develop/v2-permissions-and-consent.md).

Följ dessa instruktioner om du vill **inaktivera alla framtida användar medgivande åtgärder i hela katalogen**:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** 

3.  Klicka på **företags program** på navigerings menyn.

5.  Klicka på **användar inställningar**.

6.  Ange att **användarna ska kunna tillåta att appar får åtkomst till företags data för deras räkning** , växla till **Nej** och klicka på knappen Spara.


## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till appar](what-is-access-management.md)
