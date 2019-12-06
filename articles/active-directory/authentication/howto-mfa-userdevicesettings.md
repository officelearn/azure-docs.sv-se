---
title: Hantera användare och enheter Azure MFA – Azure Active Directory
description: Hur kan administratörer ändra användar inställningar, t. ex. tvinga användare att göra ett nytt korrektur.
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
ms.openlocfilehash: 7a1e6dc3ed1c1f9af6f6b935cdd3a6ceaedbe7a4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848263"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Hantera användar inställningar med Azure Multi-Factor Authentication i molnet

Som administratör kan du hantera följande användar-och enhets inställningar:

* Kräv att användarna anger kontakt metoder igen
* Ta bort applösenord
* Kräv MFA på alla betrodda enheter

## <a name="manage-authentication-methods"></a>Hantera autentiseringsmetoder

Som administratör har tilldelats rollen som administratör för autentisering kan du kräva att användarna återställer sina lösen ord, omregistrerar sig för MFA eller återkalla befintliga MFA-sessioner från sina användar objekt.

![Hantera autentiseringsmetoder från Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster väljer du **Azure Active Directory** > **Användare** > **Alla användare**.
1. Välj den användare som du vill utföra en åtgärd på och välj **autentiseringsmetoder**.
   - **Återställ lösen ord** kommer att återställa användarens lösen ord och tilldela ett tillfälligt lösen ord som måste ändras vid nästa inloggning.
   - **Kräv omregistrering av MFA** gör det så att när användaren loggar in nästa gång uppmanas de att konfigurera en ny MFA-autentiseringsmetod.
   - **Återkalla MFA-sessioner** rensar användarens sparade MFA-sessioner och måste utföra MFA nästa gång det krävs av principen på enheten.

## <a name="delete-users-existing-app-passwords"></a>Ta bort användare befintliga applösenord

Den här inställningen tar bort alla applösenord som en användare har skapat. Icke-webbläsarbaserade appar som kopplats till dessa applösenord slutar fungera tills ett nytt applösenord har skapats. Det krävs globala administratörs behörigheter för att utföra den här åtgärden.

### <a name="how-to-delete-users-existing-app-passwords"></a>Så här tar du bort användare befintliga applösenord

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **Användare** > **Alla användare**.
3. Till höger väljer du **Multi-Factor Authentication** i verktygsfältet. Sidan Multi-Factor Authentication öppnas.
4. Markera kryss rutan bredvid den eller de användare som du vill hantera. En lista med snabb stegs alternativ visas till höger.
5. Välj **hantera användar inställningar**.
6. Markera kryss rutan för **ta bort alla befintliga applösenord som har genererats av de valda användarna**.
   ![ta bort alla befintliga applösenord](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Klicka på **Spara**.
8. Klicka på **Stäng**.

## <a name="next-steps"></a>Nästa steg

- Få mer information om hur du [konfigurerar inställningar för Azure-Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Om användarna behöver hjälp kan du peka dem i [användar handboken för](../user-help/multi-factor-authentication-end-user.md) tvåstegsverifiering
