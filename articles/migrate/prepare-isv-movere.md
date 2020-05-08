---
title: Förbered Azure Migrate att arbeta med ISV-verktyg/arbets kraft
description: Förbered Azure Migrate att arbeta med ISV-verktyg/arbets kraft
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906987"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>Förbered för att arbeta med ISV-verktyg/driv kraft

Om du har lagt till ett [ISV-verktyg](migrate-services-overview.md#isv-integration), eller en förflyttad till ett Azure Migrate projekt, finns det några steg att förbereda innan du länkar verktyget och skicka data till Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Kontrol lera Azure AD-behörigheter

Ditt Azure-användarkonto behöver ett par behörigheter:

- Behörighet att registrera en Azure AD-App med din Azure-klient.
- Behörighet att allokera en roll till Azure AD-appen på prenumerations nivån.


## <a name="set-permissions-to-register-an-azure-ad-app"></a>Ange behörigheter för att registrera en Azure AD-App

1. I Azure Active Directory kontrollerar du rollen för ditt konto. Om du har användar rollen klickar du på **användar inställningar** till vänster och kontrollerar om användarna kan registrera program.
2. Om det är inställt på **Ja**kan alla användare i Azure AD-klienten registrera en app. Om de inte gör det kan endast administratörs användare registrera appar.
3. Om du inte har behörighet kan en administratörs användare ange ditt användar konto med rollen [program administratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) så att du kan registrera appen.
4. När verktyget är länkat till Azure Migrate kan administratören ta bort rollen från ditt konto.

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Ange behörigheter för att tilldela en roll till en Azure AD-App
 
I din Azure-prenumeration måste ditt konto ha **Microsoft. Authorization/*/Write-åtkomst**för att tilldela en roll till en Azure AD-App. 

1. Om du vill kontrol lera prenumerations behörigheter går du till Azure Portal öppna **prenumerationer**.
2. Välj relevant prenumeration. Om du inte ser det väljer du **filtret globala prenumerationer**. 
3. Välj **mina behörigheter**. Välj sedan **Klicka här om du vill visa fullständig åtkomst information för den här prenumerationen**.
4. I**vyn** **roll tilldelningar** > och kontrol lera behörigheterna.
5. Om ditt konto inte har behörigheter ber du prenumerations administratören att lägga till dig i rollen som [administratör för användar åtkomst](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) eller [ägar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rollen.
 

## <a name="start-using-the-tool"></a>Börja använda verktyget

1. Om du ännu inte har en licens eller en kostnads fri utvärderings version av verktyget går du till-verktyget i Azure Migrate, i **Registrera**, klicka på **Läs mer**.
2. I verktyget följer du anvisningarna för att länka från verktyget till Azure Migrate projektet och skicka data till Azure Migrate.

## <a name="next-steps"></a>Nästa steg

Följ de ISV-eller arbetskrafts instruktioner som du kan använda för att skicka data till Azure Migrate.

   
