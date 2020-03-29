---
title: Så här tar du bort en användares åtkomst till ett program | Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65826101"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Så här tar du bort en användares åtkomst till ett program

Den här artikeln hjälper dig att förstå hur du tar bort en användares åtkomst till ett program.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort en viss användares eller grupps tilldelning till ett program

Om du vill ta bort en användar- eller grupptilldelning till ett program följer du stegen i artikeln [Ta bort en användare eller grupp från en företagsapp i Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användarloggningar till ett program följer du stegen i artikeln [Inaktivera användarloggningar för en företagsapp i Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

Så här tar du **bort ett program:**

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Markera det program som du vill ta bort.

7. När programmet har läses in klickar du på **Ikonen Ta bort** från det övre programmets **översiktsfönster.**

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida åtgärder för användarsamtycke till alla program

Om du inaktiverar användarens medgivande för hela katalogen hindra slutanvändare från att samtycka till något program. Administratörer kan fortfarande medgivande för användarens räkning. Mer information om programmets medgivande och varför du kanske eller kanske inte vill göra detta, [läs Förstå användarens och administratörens samtycke](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Se även [Behörigheter och medgivande](../develop/v2-permissions-and-consent.md).

Om du vill **inaktivera alla framtida åtgärder för användarmedgivande i hela katalogen**följer du dessa instruktioner:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** 

3.  Klicka på **Företagsprogram** på navigeringsmenyn.

5.  Klicka på **Användarinställningar**.

6.  Ange **att användarna kan tillåta appar att komma åt företagsdata för deras räkning** växla till **Nej** och klicka på knappen Spara.


## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till appar](what-is-access-management.md)
