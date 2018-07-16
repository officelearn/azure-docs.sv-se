---
title: Administratörer hantera användare och enheter – Azure MFA | Microsoft Docs
description: Här beskrivs hur du ändrar användarinställningar, till exempel tvinga användare att göra processen proof-up igen.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/23/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 3abe858c61eb47a9b0174461275c39acdf71a273
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054394"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Hantera användarinställningar med Azure Multi-Factor Authentication i molnet

Du kan hantera följande användar- och inställningar som en administratör:

* Kräv att användare ska uppge kontaktmetoder igen
* Ta bort applösenord
* Kräva MFA för alla betrodda enheter 

## <a name="require-users-to-provide-contact-methods-again"></a>Kräv att användare ska uppge kontaktmetoder igen
Den här inställningen Tvingar användaren att slutföra registreringsprocessen igen. Icke-webbläsarappar fortsätta att fungera om användaren har applösenord för dessa.  Du kan ta bort applösenord för användare genom att markera **ta bort alla befintliga applösenord som de valda användarna**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Hur användare ska uppge kontaktmetoder igen
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**. Multifaktorautentiseringssidan öppnas. 
4. Markera kryssrutan bredvid den eller de användare som du vill hantera. En lista med snabba steg alternativ visas till höger. 
5. Välj **hantera användarinställningar**.
6. Markera kryssrutan för **Kräv att valda användare ska uppge kontaktmetoder igen**.
   ![Uppge kontaktmetoder](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Klicka på **Spara**.
8. Klicka på **Stäng**.

## <a name="delete-users-existing-app-passwords"></a>Ta bort användare som har befintliga applösenord
Den här inställningen tar bort alla applösenord som en användare har skapat. Icke-webbläsarbaserade appar som är kopplade till dessa applösenord upphör att fungera tills ett nytt applösenord har skapats.

### <a name="how-to-delete-users-existing-app-passwords"></a>Ta bort användare som har befintliga applösenord
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**. Multifaktorautentiseringssidan öppnas. 
6. Markera kryssrutan bredvid den eller de användare som du vill hantera. En lista med snabba steg alternativ visas till höger. 
7. Välj **hantera användarinställningar**.
8. Markera kryssrutan för **ta bort alla befintliga applösenord som de valda användarna**.
   ![Ta bort applösenord](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
9. Klicka på **Spara**.
10. Klicka på **Stäng**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Återställ MFA på alla sparade enheter för en användare
En av de konfigurerbara funktionerna i Azure Multi-Factor Authentication ger användarna kan markera enheter som betrodda. Mer information finns i [konfigurera Azure Multi-Factor Authentication-inställningar](howto-mfa-mfasettings.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Användare kan välja bort tvåstegsverifiering för ett konfigurerbart antal dagar för sina vanliga enheter. Om ett konto komprometteras eller en betrodd enhet tappas bort, behöver du för att kunna ta bort den betrodda statusen och kräva tvåstegsverifiering igen.

Den **Återställ multifaktorautentisering på alla sparade enheter** måste inställningen innebär att användaren utför en tvåstegsverifiering nästa gång de loggar in, oavsett om de har valt att markera sin enhet som betrodda. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Så här återställer du MFA på alla pausade enheter för en användare
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**. Multifaktorautentiseringssidan öppnas. 
6. Markera kryssrutan bredvid den eller de användare som du vill hantera. En lista med snabba steg alternativ visas till höger. 
7. Välj **hantera användarinställningar**.
8. Markera kryssrutan för **Återställ multifaktorautentisering på alla sparade enheter**
   ![ta bort applösenord](./media/howto-mfa-userdevicesettings/rememberdevices.png)
9. Klicka på **Spara**.
10. Klicka på **Stäng**.

## <a name="next-steps"></a>Nästa steg

- Få mer information om hur du [konfigurera Azure Multi-Factor Authentication-inställningar](howto-mfa-mfasettings.md)

- Om dina användare behöver hjälp kan peka dem mot den [användarhandboken för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user.md)
