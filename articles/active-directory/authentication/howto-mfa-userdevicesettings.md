---
title: Administratörer hanterar användare och enheter – Azure MFA – Azure Active Directory
description: Hur kan administratörer ändra användar inställningar, t. ex. tvinga användare att göra ett nytt korrektur.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3152dead04510078dd475b611afbfc30264e58f7
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297640"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Hantera användar inställningar med Azure Multi-Factor Authentication i molnet

Som administratör kan du hantera följande användar-och enhets inställningar:

* Kräv att användarna anger kontakt metoder igen
* Ta bort applösenord
* Kräv MFA på alla betrodda enheter

## <a name="manage-authentication-methods"></a>Hantera autentiseringsmetoder

Som administratör har tilldelats rollen som administratör för autentisering kan du kräva att användarna återställer sina lösen ord, omregistrerar sig för MFA eller återkalla befintliga MFA-sessioner från sina användar objekt.

![Hantera autentiseringsmetoder från Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

## <a name="require-users-to-provide-contact-methods-again"></a>Kräv att användarna anger kontakt metoder igen

Den här inställningen tvingar användaren att slutföra registrerings processen igen. Icke-webbläsarbaserade appar fortsätter att fungera om användaren har applösenord för dem.  Du kan ta bort användarnas applösenord genom att även välja **ta bort alla befintliga applösenord som har genererats av de valda användarna**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Så här kräver du att användarna anger kontakt metoder igen

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > användarealla > **användare**till vänster.
3. Välj **Multi-Factor Authentication** i verktygsfältet till höger. Sidan Multi-Factor Authentication öppnas.
4. Markera kryss rutan bredvid den eller de användare som du vill hantera. En lista med snabb stegs alternativ visas till höger.
5. Välj **hantera användar inställningar**.
6. Markera kryss rutan för **Kräv att valda användare ska tillhandahålla kontakt metoder igen**.
   ![Kräv att användarna anger kontakt metoder igen](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Klicka på **Spara**.
8. Klicka på **Stäng**.

Organisationer kan utföra de här stegen med PowerShell med hjälp av följande som en guide för `StrongAuthenticationMethods` att rensa attributet:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Ta bort användare befintliga applösenord

Den här inställningen tar bort alla applösenord som en användare har skapat. Icke-webbläsarbaserade appar som kopplats till dessa applösenord slutar fungera tills ett nytt applösenord har skapats.

### <a name="how-to-delete-users-existing-app-passwords"></a>Så här tar du bort användare befintliga applösenord

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > användarealla > **användare**till vänster.
3. Välj **Multi-Factor Authentication** i verktygsfältet till höger. Sidan Multi-Factor Authentication öppnas.
4. Markera kryss rutan bredvid den eller de användare som du vill hantera. En lista med snabb stegs alternativ visas till höger.
5. Välj **hantera användar inställningar**.
6. Markera kryss rutan för **ta bort alla befintliga applösenord som har genererats av de valda användarna**.
   ![Ta bort alla befintliga applösenord](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Klicka på **Spara**.
8. Klicka på **Stäng**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Återställ MFA på alla sparade enheter för en användare

En av de konfigurerbara funktionerna i Azure Multi-Factor Authentication ger användarna möjlighet att markera enheter som betrodda. Mer information finns i [Konfigurera inställningar för Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Användare kan välja mellan tvåstegsverifiering för ett konfigurerbart antal dagar på sina vanliga enheter. Om ett konto har komprometterats eller en betrodd enhet förloras måste du kunna ta bort den betrodda statusen och kräva tvåstegsverifiering igen.

När det här alternativet är markerat måste du **återställa Multi-Factor Authentication på alla sparade enheter** -användare för att utföra tvåstegsverifiering nästa gång de loggar in, även om de har markerat att enheten är betrodd.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Så här återställer du MFA på alla inaktiverade enheter för en användare

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > användarealla > **användare**till vänster.
3. Välj **Multi-Factor Authentication** i verktygsfältet till höger. Sidan Multi-Factor Authentication öppnas.
4. Markera kryss rutan bredvid den eller de användare som du vill hantera. En lista med snabb stegs alternativ visas till höger.
5. Välj **hantera användar inställningar**.
6. Markera kryss rutan för att **återställa Multi-Factor Authentication på alla sparade enheter**
   ![Återställ Multi-Factor Authentication på alla sparade enheter](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Klicka på **Spara**.
8. Klicka på **Stäng**.

## <a name="next-steps"></a>Nästa steg

- Få mer information om hur du [konfigurerar inställningar för Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Om användarna behöver hjälp kan du peka dem i [användar handboken för](../user-help/multi-factor-authentication-end-user.md) tvåstegsverifiering
