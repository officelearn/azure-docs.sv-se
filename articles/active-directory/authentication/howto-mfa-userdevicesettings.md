---
title: Hantera användarinställningar för Azure Multi-Factor Authentication - Azure Active Directory
description: Lär dig hur du kan konfigurera Azure Active Directory-användarinställningar för Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309758"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Hantera användarinställningar för Azure Multi-Factor-autentisering

För att hantera användare av Azure Multi-Factor Authentication kan du kräva att användare återställer sitt lösenord, registrerar om för MFA eller återkallar befintliga MFA-sessioner. För användare som har definierat applösenord kan du också välja att ta bort dessa lösenord, vilket gör att äldre autentisering misslyckas i dessa program. Dessa åtgärder kan vara nödvändiga om du behöver ge hjälp till en användare eller vill återställa deras säkerhetsstatus.

## <a name="manage-user-authentication-options"></a>Hantera alternativ för användarautentisering

Om du har tilldelat rollen *Autentiseringsadministratör* kan du kräva att användare återställer sitt lösenord, registrerar om för MFA eller återkallar befintliga MFA-sessioner från sitt användarobjekt. Så här hanterar du användarinställningarna:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster väljer du **Azure Active Directory** > **Users** > **All users**.
1. Välj den användare du vill utföra en åtgärd på och välj **Autentiseringsmetoder**. Högst upp i fönstret väljer du ett av följande alternativ för användaren:
   - **Återställ lösenord** återställer användarens lösenord och tilldelar ett tillfälligt lösenord som måste ändras vid nästa inloggning.
   - **Kräv Re-register MFA** gör det så att när användaren loggar in nästa gång, de uppmanas att ställa in en ny MFA autentiseringsmetod.
   - **Återkalla MFA-sessioner** rensar användarens ihågkomna MFA-sessioner och kräver att de utför MFA nästa gång det krävs av principen på enheten.

   ![Hantera autentiseringsmetoder från Azure-portalen](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Ta bort befintliga applösenord för användare

Om det behövs kan du ta bort alla applösenord som en användare har skapat. Appar som inte är webbläsare som var associerade med dessa applösenord slutar fungera tills ett nytt applösenord har skapats. *Globala* administratörsbehörigheter krävs för att utföra den här åtgärden.

Så här tar du bort en användares applösenord:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. På vänster sida väljer du **Azure Active Directory** > **Users** > **All users**.
1. Välj **Multifaktorautentisering**. Du kan behöva bläddra till höger för att se det här menyalternativet. Välj exempel skärmdump nedan för att se hela Azure portal fönster och menyplats:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Välj Multifaktorautentisering i fönstret Användare i Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Markera rutan bredvid den eller de användare som du vill hantera. En lista med snabbstegsalternativ visas till höger.
1. Välj **Hantera användarinställningar**och markera sedan kryssrutan För **Ta bort alla befintliga applösenord**som genereras av de markerade användarna , vilket visas i följande exempel: ![Ta bort alla befintliga applösenord](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Välj **spara**och **stäng**sedan .

## <a name="next-steps"></a>Nästa steg

Den här artikeln hjälpte till att konfigurera enskilda användarinställningar. Information om hur du konfigurerar tjänstinställningar för Azure Multi Factor Authentication finns i [Konfigurera azure multifaktorautentiseringsinställningar](howto-mfa-mfasettings.md)

Om användarna behöver hjälp läser du [användarhandboken för Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md).
