---
title: Administratörer hantera användare och enheter – Azure MFA | Microsoft Docs
description: Här beskrivs hur du ändrar användarinställningar, till exempel att tvinga användare att göra bevis upp processen igen.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/23/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 363106421e75fa2e1b220d03a7d7cbed25447bcc
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098404"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Hantera användarinställningar med Azure Multi-Factor Authentication i molnet

Du kan hantera följande inställningar för användare och enhet som en administratör:

* Kräv att användare ska uppge kontaktmetoder igen
* Ta bort applösenord
* Kräva MFA på alla betrodda enheter 

## <a name="require-users-to-provide-contact-methods-again"></a>Kräv att användare ska uppge kontaktmetoder igen
Den här inställningen gör att slutföra registreringsprocessen igen. Icke-webbläsarappar fortsätta att fungera om användaren har applösenord för dessa.  Du kan ta bort applösenord för användare genom att markera **ta bort alla befintliga applösenord som de valda användarna**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Hur kan kräva att användare ska uppge kontaktmetoder igen
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**. Multifaktorautentiseringssidan öppnas. 
4. Markera kryssrutan bredvid en eller flera användare som du vill hantera. En lista över snabbsteg alternativ visas till höger. 
5. Välj **hantera användarinställningar**.
6. Markera kryssrutan för **Kräv att valda användare ska uppge kontaktmetoder igen**.
   ![Uppge kontaktmetoder](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Klicka på **Spara**.
8. Klicka på **Stäng**.

## <a name="delete-users-existing-app-passwords"></a>Ta bort användare som befintliga applösenord
Den här inställningen tar bort alla applösenord som en användare har skapat. Icke-webbläsarbaserade appar som har kopplats till dessa applösenord upphör att fungera förrän ett nytt applösenord har skapats.

### <a name="how-to-delete-users-existing-app-passwords"></a>Ta bort användare som befintliga applösenord
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**. Multifaktorautentiseringssidan öppnas. 
6. Markera kryssrutan bredvid en eller flera användare som du vill hantera. En lista över snabbsteg alternativ visas till höger. 
7. Välj **hantera användarinställningar**.
8. Markera kryssrutan för **ta bort alla befintliga applösenord som de valda användarna**.
   ![Ta bort applösenord](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
9. Klicka på **Spara**.
10. Klicka på **Stäng**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Återställ Multifaktorautentisering på alla sparade enheter för en användare
En av de konfigurerbara funktionerna i Azure Multi-Factor Authentication ge användarna kan markera enheter som betrodd. Mer information finns i [konfigurerar Azure Multi-Factor Authentication-inställningar](howto-mfa-mfasettings.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Användare kan välja bort tvåstegsverifiering för antalet dagar på sina enheter för vanliga. Om ett konto komprometteras eller en betrodd enhet tappas bort, måste du kunna ta bort den betrodda statusen och kräver tvåstegsverifiering igen.

Den **Återställ multifaktorautentisering på alla sparade enheter** inställningen innebär att användaren kommer att anropas för att utföra tvåstegsverifiering nästa gång de loggar in, oavsett om de har valt att markera sin enhet som betrodda. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Så här återställer du MFA på alla avbrutna enheter för en användare
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**. Multifaktorautentiseringssidan öppnas. 
6. Markera kryssrutan bredvid en eller flera användare som du vill hantera. En lista över snabbsteg alternativ visas till höger. 
7. Välj **hantera användarinställningar**.
8. Markera kryssrutan för **Återställ multifaktorautentisering på alla sparade enheter**
   ![ta bort applösenord](./media/howto-mfa-userdevicesettings/rememberdevices.png)
9. Klicka på **Spara**.
10. Klicka på **Stäng**.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du [konfigurerar Azure Multi-Factor Authentication-inställningar](howto-mfa-mfasettings.md)

- Om dina användare behöver hjälp peka dem mot den [användarhandboken för tvåstegsverifiering](end-user/current/multi-factor-authentication-end-user.md)
