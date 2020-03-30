---
title: Hantera användare och enheter Azure MFA - Azure Active Directory
description: Hur kan administratörer ändra användarinställningar som att tvinga användarna att göra korrekturprocessen igen.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07845bb5b742b1bcfbb22d260457e9a8e16edab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263717"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Hantera användarinställningar med Azure Multi-Factor Authentication i molnet

Som administratör kan du hantera följande användar- och enhetsinställningar:

* Kräv att användarna tillhandahåller kontaktmetoder igen
* Ta bort applösenord
* Kräv MFA på alla betrodda enheter

## <a name="manage-authentication-methods"></a>Hantera autentiseringsmetoder

Som administratör som tilldelats rollen Autentiseringsadministratör kan du kräva att användare återställer sitt lösenord, registrerar om för MFA eller återkallar befintliga MFA-sessioner från sitt användarobjekt.

![Hantera autentiseringsmetoder från Azure-portalen](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster väljer du **Azure Active Directory** > **Users** > **All users**.
1. Välj den användare du vill utföra en åtgärd på och välj **Autentiseringsmetoder**.
   - **Återställ lösenord** återställer användarens lösenord och tilldelar ett tillfälligt lösenord som måste ändras vid nästa inloggning.
   - **Kräv Omregistrera MFA** kommer att göra det så att när användaren loggar in nästa gång, kommer de att uppmanas att ställa in en ny MFA autentiseringsmetod.
   - **Återkalla MFA-sessioner** rensar användarens ihågkomna MFA-sessioner och kräver att de utför MFA nästa gång det krävs av principen på enheten.

## <a name="delete-users-existing-app-passwords"></a>Ta bort befintliga applösenord för användare

Den här inställningen tar bort alla applösenord som en användare har skapat. Appar som inte är webbläsare som var associerade med dessa applösenord slutar fungera tills ett nytt applösenord har skapats. Globala administratörsbehörigheter krävs för att utföra den här åtgärden.

### <a name="how-to-delete-users-existing-app-passwords"></a>Så här tar du bort befintliga applösenord för användare

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **Users** > **All users**.
3. Till höger väljer du **Multifaktorautentisering** i verktygsfältet. Sidan för multifaktorautentisering öppnas.
4. Markera rutan bredvid den eller de användare som du vill hantera. En lista med snabbstegsalternativ visas till höger.
5. Välj **Hantera användarinställningar**.
6. Markera kryssrutan Ta **bort alla befintliga applösenord som genereras av de markerade användarna**.
   ![Ta bort alla befintliga applösenord](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Klicka på **Spara**.
8. Klicka **på stäng**.

## <a name="next-steps"></a>Nästa steg

- Få mer information om hur [du konfigurerar azure multifaktorautentiseringsinställningar](howto-mfa-mfasettings.md)
- Om användarna behöver hjälp pekar du dem mot [användarhandboken för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user.md)
