---
title: Lösen ords återsignering med Microsoft Authenticator app-Azure Active Directory
description: Aktivera lösen ords lös inloggning till Azure AD med hjälp av Microsoft Authenticator-appen (förhands granskning)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b4792e73f6326bb9ac67ce3aabe10b8314bb826
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568222"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Aktivera lösen ords utan lösen ord med Microsoft Authenticator-appen (förhands granskning)

Microsoft Authenticator-appen kan användas för att logga in på ett Azure AD-konto utan att använda ett lösen ord. På samma sätt som med [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification), använder Microsoft Authenticator nyckelbaserad autentisering för att aktivera autentiseringsuppgifter som är knutna till en enhet och använder bio metrisk eller PIN-kod. Den här autentiseringsmetoden kan användas på valfri enhets plattform, inklusive mobil, och med appar eller webbplatser som integreras med Microsoft Authentication Libraries.

![Exempel på en webb läsar inloggning som ber användaren att godkänna inloggningen](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

I stället för att se en uppmaning om att ange ett lösen ord när du har angett ett användar namn, ser en person som har aktiverat telefon inloggning från Microsoft Authenticator-appen ett meddelande som uppmanar dem att trycka ett nummer i appen. För att slutföra inloggnings processen i appen måste användaren matcha numret, välja **Godkänn**och sedan ange PIN-koden eller bio metriskt.

## <a name="prerequisites"></a>Krav

Om du vill använda lösen ords lös inloggning med Microsoft Authenticator-appen måste följande krav vara uppfyllda:

- Azure Multi-Factor Authentication, med push-meddelanden som är tillåtna som en verifieringsmetod.
- Den senaste versionen av Microsoft Authenticator installerad på enheter som kör iOS 8,0 eller senare, eller Android 6,0 eller senare.

> [!NOTE]
> Om du har aktiverat Microsoft Authenticator automatisk inloggning för lösen ords granskning med hjälp av Azure AD PowerShell var det aktiverat för hela katalogen. Om du aktiverar med den här nya metoden ersätter den PowerShell-principen. Vi rekommenderar att du aktiverar för alla användare i din klient via menyn nya *autentiseringsmetoder* , annars kommer användare som inte är i den nya principen inte längre att kunna logga in utan lösen ord.

## <a name="enable-passwordless-authentication-methods"></a>Aktivera metoder för lösen ords kryptering

Om du vill använda lösenordsbaserad autentisering i Azure AD måste du först aktivera den kombinerade registrerings upplevelsen och sedan Aktivera användare för metoden lösen ord mindre.

### <a name="enable-the-combined-registration-experience"></a>Aktivera kombinerad registrerings upplevelse

Registrerings funktioner för metoder för lösen ords skydd förlitar sig på den kombinerade registrerings funktionen. Om du vill låta användarna slutföra den kombinerade registreringen själva följer du stegen för att [Aktivera kombinerad registrering av säkerhets information](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Aktivera autentiseringsmetoder för inloggning med lösen ord

Med Azure AD kan du välja vilka autentiseringsmetoder som ska användas under inloggnings processen. Användarna registrerar sig sedan för de metoder som de vill använda.

Utför följande steg för att aktivera autentiseringsmetoden för inloggning med lösen ord utan lösen ord:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett *globalt administratörs* konto.
1. Sök efter och välj *Azure Active Directory*och bläddra sedan till autentiseringsmetoder för **säkerhets**  >  **autentiseringsmetoder**  >  **(för hands version)**
1. Under **lösen ords telefon inloggning**väljer du följande alternativ:
   1. **Aktivera** – Ja eller Nej
   1. **Mål** – alla användare eller Välj användare
1. Välj **Spara**om du vill tillämpa den nya principen.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Användar registrering och hantering av Microsoft Authenticator app

Med den lösenordsskyddade autentiseringsmetoden som är tillgänglig för användning i Azure AD måste användarna nu registrera sig för metoden för lösen ords Avhjälp med hjälp av följande steg:

1. Bläddra till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Logga in och Lägg sedan till Authenticator-appen genom att välja **Lägg till metod > Authenticator-app**och sedan **lägga till**.
1. Följ anvisningarna för att installera och konfigurera appen Microsoft Authenticator på enheten.
1. Välj **klar** för att slutföra konfigurationen av autentisering.
1. I **Microsoft Authenticator** app väljer du **Aktivera telefonin loggning** på den nedrullningsbara menyn för det konto som har registrerats.
1. Följ instruktionerna i appen för att slutföra registreringen av kontot för inloggning med lösen ord.

Organisationer kan dirigera sina användare att [Logga in med din telefon, inte ditt lösen ord](../user-help/user-help-auth-app-sign-in.md) för att få ytterligare hjälp med att konfigurera Microsoft Authenticator-appen och aktivera telefonin loggning.

> [!NOTE]
> Användare som inte tillåts enligt principen att använda telefonin loggning kan inte längre aktivera det i Microsoft Authenticator-appen.  

## <a name="sign-in-with-passwordless-credential"></a>Logga in med autentiseringsuppgifter för lösen ord

En användare kan börja använda lösen ords lös inloggning när en administratör har aktiverat sin klient och användaren har uppdaterat sin Microsoft Authenticator app för att aktivera telefonin loggning.

Om du vill börja använda telefonin loggning när du har skrivit ett användar namn på inloggnings sidan och väljer **Nästa**, kan användarna behöva välja **andra sätt att logga in**och sedan **godkänna en begäran på min Microsoft Authenticator-app**. Användarna visas sedan med ett nummer och de uppmanas att välja rätt nummer i stället för att använda sina lösen ord i sina Microsoft Authenticator-appar. När användarna har använt inloggning med lösen ord uppmanas de att använda den igen, tills de använder Välj en annan metod.

![Exempel på en webbläsares inloggning med Microsoft Authenticator-appen](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Kända problem

Följande kända problem finns i den aktuella för hands versionen.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Kan inte se alternativ för telefonin loggning för lösen ord

Om en användare har en svars lös inloggnings verifiering för inloggning väntar och försöker logga in igen, kan användaren bara se ett alternativ för att ange ett lösen ord i stället. Öppna Microsoft Authenticator och svara på eventuella meddelande-prompter om du vill fortsätta använda lösen ords lös telefonin loggning.

### <a name="federated-accounts"></a>Federerade konton

När en användare har aktiverat lösen ords lös autentiseringsuppgifter slutar Azure AD-inloggningarna att använda login_hint för att påskynda användaren till en extern inloggnings plats. Den här logiken förhindrar att användare i en hybrid klient dirigeras till AD FS för inloggnings verifiering utan att användaren vidtar ytterligare steg för att klicka på Använd lösen ordet i stället.

### <a name="azure-mfa-server"></a>Azure MFA-Server

Slutanvändare som har Aktiver ATS för MFA via en organisations lokala Azure MFA-Server kan fortfarande skapa och använda en enda inloggnings inloggning för lösen ord. Om användaren försöker uppgradera flera installationer (5 +) av Microsoft Authenticator med autentiseringsuppgifterna kan denna ändring resultera i ett fel.  

### <a name="device-registration"></a>Enhetsregistrering

En av kraven för att skapa den här nya starka autentiseringsuppgiften är att enheten, där Microsoft Authenticator-appen är installerad, också måste registreras i Azure AD-klienten till en enskild användare. På grund av aktuella begränsningar för enhets registrering kan en enhet bara registreras i en enda klient. Den här gränsen innebär att endast ett arbets-eller skol konto i Microsoft Authenticator-appen kan aktive ras för telefonin loggning.

> [!NOTE]
> Enhets registrering är inte detsamma som enhets hantering eller MDM. Den kopplar bara ett enhets-ID och ett användar-ID tillsammans i Azure AD-katalogen.  

## <a name="next-steps"></a>Nästa steg

Information om Azure AD-autentisering och lösen ords lös metoder finns i följande artiklar:

* [Lär dig hur lösen ords ingen autentisering fungerar](concept-authentication-passwordless.md)
* [Läs mer om enhets registrering](../devices/overview.md#getting-devices-in-azure-ad)
* [Lär dig mer om Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
