---
title: Lösen ords återsignering med Microsoft Authenticator app-Azure Active Directory
description: Aktivera lösen ords lös inloggning till Azure AD med hjälp av Microsoft Authenticator-appen (förhands granskning)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 100e4b88589f3731d127ccb1060e556c1f3a2f39
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413254"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Aktivera lösen ords utan lösen ord med Microsoft Authenticator-appen (förhands granskning)

Microsoft Authenticator-appen kan användas för att logga in på ett Azure AD-konto utan att använda ett lösen ord. På samma sätt som med [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification), använder Microsoft Authenticator nyckelbaserad autentisering för att aktivera autentiseringsuppgifter som är knutna till en enhet och använder bio metrisk eller PIN-kod. Den här autentiseringsmetoden kan användas på valfri enhets plattform, inklusive mobil, och med appar eller webbplatser som integreras med Microsoft Authentication Libraries. 

![Exempel på en webb läsar inloggning som ber användaren att godkänna inloggningen](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

I stället för att se en uppmaning om att ange ett lösen ord när du har angett ett användar namn, kommer en person som har aktiverat telefonin loggning från Microsoft Authenticator-appen att se ett meddelande som säger att de trycker på ett nummer i appen. Användaren måste matcha siffran i appen, välja Godkänn, ange sin PIN-kod eller bio metrisk, så slutförs autentiseringen.

> [!NOTE]
> Den här funktionen har funnits i Microsoft Authenticator app sedan mars 2017, så det finns en risk att när principen är aktive rad för en katalog kan användarna stöta på det här flödet omedelbart och se ett fel meddelande om de inte har Aktiver ATS av en princip. Tänk på och Förbered dina användare för den här ändringen.

## <a name="prerequisites"></a>Förutsättningar

- Azure Multi-Factor Authentication, med push-meddelanden som är tillåtna som en verifieringsmetod 
- Den senaste versionen av Microsoft Authenticator installerad på enheter som kör iOS 8,0 eller senare, eller Android 6,0 eller senare.

> [!NOTE]
> Om du har aktiverat den tidigare Microsoft Authenticator för lösen ords återhands granskning med Azure AD PowerShell var den aktive rad för hela katalogen. Om du aktiverar med den här nya metoden kommer den att Supercede PowerShell-principen. Vi rekommenderar att du aktiverar för alla användare i din klient via de nya autentiseringsmetoderna, annars kommer användare som inte är i den nya principen inte längre att kunna logga in passwordlessly. 

## <a name="enable-passwordless-authentication-methods"></a>Aktivera metoder för lösen ords kryptering

### <a name="enable-the-combined-registration-experience"></a>Aktivera kombinerad registrerings upplevelse

Registrerings funktioner för metoder för lösen ords skydd förlitar sig på den kombinerade registrerings funktionen. Följ stegen i artikeln [Aktivera kombinerad registrering av säkerhets information](howto-registration-mfa-sspr-combined.md)för att aktivera kombinerad registrering.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Aktivera autentiseringsmetoder för inloggning med lösen ord

1. Logga in på [Azure Portal](https://portal.azure.com)
1. Sök efter och välj *Azure Active Directory*. Välj autentiseringsmetod för **säkerhets**  >  **metoder**  >  **(för hands version)**
1. Under **lösen ords telefon inloggning**väljer du följande alternativ
   1. **Aktivera** – Ja eller Nej
   1. **Mål** – alla användare eller Välj användare
1. **Spara** för att ange den nya principen

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Användar registrering och hantering av Microsoft Authenticator app

1. Bläddra till[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Logga in om inte redan
1. Lägg till en Authenticator-app genom att klicka på **Lägg till metod**, Välj **Authenticator-app**och klicka på **Lägg till**
1. Följ anvisningarna för att installera och konfigurera appen Microsoft Authenticator på din enhet
1. Klicka på **klar** för att slutföra installations flödet för autentisera MFA-appen. 
1. I **Microsoft Authenticator**väljer du **Aktivera telefonin loggning** från List menyn konto
1. Följ instruktionerna i appen för att slutföra registreringen för inloggning med lösen ord. 

Organisationer kan peka sina användare till artikeln [Logga in med din telefon, inte ditt lösen ord](../user-help/microsoft-authenticator-app-phone-signin-faq.md) för ytterligare hjälp att konfigurera i Microsoft Authenticator-appen och aktivera telefonin loggning. För att kunna tillämpa de här inställningarna kan du behöva logga ut och logga in på klienten igen. 

## <a name="sign-in-with-passwordless-credential"></a>Logga in med autentiseringsuppgifter för lösen ord

För offentlig för hands version finns det inget sätt att tvinga användare att skapa eller använda den här nya autentiseringsuppgiften. En användare kommer bara att stöta på lösen ords lös inloggning när en administratör har aktiverat sin klient **och** användaren har uppdaterat sin Microsoft Authenticator app för att aktivera telefonin loggning.

När du har angett ditt användar namn på webben och valt **Nästa**, visas användare med ett nummer och de Microsoft Authenticator uppmanas att välja lämpligt antal att autentisera i stället för att använda lösen ordet. 

![Exempel på en webbläsares inloggning med Microsoft Authenticator-appen](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Kända problem

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>Användaren är inte aktive rad av en princip men ändå har inloggnings metoden för lösen ord utan lösen ord i Microsoft Authenticator

Det är möjligt att en användare har en viss punkt som har skapat autentiseringsuppgifter för lösen ord för inloggning i den aktuella Microsoft Authenticator appen eller på en tidigare enhet. När en administratör aktiverar principen för autentisering av lösen ord för lösen ord, kommer alla användare med registrerade autentiseringsuppgifter att börja uppleva den nya inloggnings frågan, oavsett om de har Aktiver ATS för att använda principen eller inte. Om användaren inte har tillåtits att använda autentiseringsuppgiften av en princip visas ett fel meddelande när du har slutfört autentiserings flödet. 

Administratören kan välja att göra det möjligt för användaren att använda lösen ords lös telefonin loggning eller att användaren måste ta bort metoden. Om användaren inte längre har den registrerade enheten kan de gå till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) och ta bort den. Om de fortfarande använder autentiseraren för MFA kan de välja **inaktivera telefonin loggning** inifrån Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>AD FS-integrering

När en användare har aktiverat Microsoft Authenticator lösen ords lös autentiseringsuppgifter kommer autentiseringen för användaren alltid att vara standard att skicka ett meddelande för godkännande. Den här logiken förhindrar att användare i en hybrid klient dirigeras till AD FS för inloggnings verifiering utan att användaren vidtar ytterligare steg för att klicka på Använd lösen ordet i stället. Den här processen kringgår också alla lokala principer för villkorlig åtkomst och genom strömnings flöden. 

Om en användare har en svars lös inloggnings verifiering som väntar på lösen ord och försöker logga in igen, kan det hända att användaren AD FS ange ett lösen ord i stället.  

### <a name="azure-mfa-server"></a>Azure MFA-Server

Slutanvändare som har Aktiver ATS för MFA via en organisations lokala Azure MFA-Server kan fortfarande skapa och använda ett enda lösen ord för lösen ords lös inloggning. Om användaren försöker uppgradera flera installationer (5 +) av Microsoft Authenticator med autentiseringsuppgifterna kan denna ändring resultera i ett fel.  

### <a name="device-registration"></a>Enhetsregistrering

En av kraven för att skapa den här nya starka autentiseringsuppgiften är att enheten, där Microsoft Authenticator-appen är installerad, också måste registreras i Azure AD-klienten till en enskild användare. På grund av aktuella begränsningar för enhets registrering kan en enhet bara registreras i en enda klient. Den här gränsen innebär att endast ett arbets-eller skol konto i Microsoft Authenticator-appen kan aktive ras för telefonin loggning.

### <a name="intune-mobile-application-management"></a>Hantering av mobil program i Intune 

Slutanvändare som omfattas av en princip som kräver hantering av mobil program (MAM) kan inte registrera lösen ords lös autentiseringsuppgifter i Microsoft Authenticator-appen. 

> [!NOTE]
> Enhets registrering är inte detsamma som enhets hantering eller MDM. Den kopplar bara ett enhets-ID och ett användar-ID tillsammans i Azure AD-katalogen.  

## <a name="next-steps"></a>Nästa steg

[Vad är lösenordsfritt?](concept-authentication-passwordless.md)

[Läs mer om enhets registrering](../devices/overview.md#getting-devices-in-azure-ad)

[Lär dig mer om Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
