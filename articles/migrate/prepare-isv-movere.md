---
title: Förbereda Azure Migrate att arbeta med ett ISV-verktyg/arbets kraft
description: Den här artikeln beskriver hur du förbereder Azure Migrate att arbeta med ett ISV-verktyg eller en arbets kraft och hur du börjar använda verktyget.
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9513e783d4f9d7be83f1434d4dd9011844af8993
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682645"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Förbered för att arbeta med ett ISV-verktyg eller en arbets kraft

Om du har lagt till ett [ISV-verktyg](migrate-services-overview.md#isv-integration) eller en förflyttad till ett Azure Migrate projekt, finns det några steg att följa innan du länkar verktyget och skickar data till Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Kontrol lera Azure AD-behörigheter

Ditt Azure-användarkonto behöver följande behörigheter:

- Behörighet att registrera en Azure Active Directory (Azure AD)-app med din Azure-klient
- Behörighet att allokera en roll till Azure AD-appen på prenumerations nivån


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Ange behörigheter för att registrera en Azure AD-App

1. I Azure AD kontrollerar du rollen för ditt konto.
2. Om du har användar rollen väljer du **användar inställningar** till vänster och kontrollerar om användarna kan registrera program. Om det är inställt på **Ja**kan alla användare i Azure AD-klienten registrera en app. Om det är inställt på **Nej**kan endast administratörs användare registrera appar.   
3. Om du inte har behörighet kan en administratörs användare ange ditt användar konto med rollen [program administratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) så att du kan registrera appen.
4. När verktyget är länkat till Azure Migrate kan administratören ta bort rollen från ditt konto.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Ange behörigheter för att tilldela en roll till en Azure AD-App
 
I din Azure-prenumeration måste ditt konto ha **Microsoft. Authorization/*/Write-** åtkomst för att tilldela en roll till en Azure AD-App. 

1. Öppna **prenumerationer**i Azure Portal.
2. Välj relevant prenumeration. Om du inte ser det väljer du **filtret globala prenumerationer**. 
3. Välj **mina behörigheter**. Välj sedan **Klicka här om du vill visa fullständig åtkomst information för den här prenumerationen**.
4. **Role assignments**  >  Kontrol lera behörigheterna i**vyn**roll tilldelningar. Om ditt konto inte har behörigheter ber du prenumerations administratören att lägga till dig i rollen som [administratör för användar åtkomst](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) eller [ägar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rollen.
 

## <a name="start-using-the-tool"></a>Börja använda verktyget

1. Om du ännu inte har en licens eller en kostnads fri utvärderings version av verktyget går du till verktygs posten i Azure Migrate, i **Registrera**, och väljer **Läs mer**.
2. I verktyget följer du anvisningarna för att länka från verktyget till Azure Migrate projektet och skicka data till Azure Migrate.

## <a name="next-steps"></a>Nästa steg

Följ instruktionerna från din ISV eller din arbets kraft för att skicka data till Azure Migrate.

   
