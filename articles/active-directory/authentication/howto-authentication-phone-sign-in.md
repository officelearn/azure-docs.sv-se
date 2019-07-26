---
title: Lösen ords delning med Microsoft Authenticator app (för hands version) – Azure Active Directory
description: Logga in på Azure AD med hjälp av Microsoft Authenticator-appen utan att använda ditt lösen ord (offentlig för hands version)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c464874708c7b93ec5620cc9ae253912ce1a4790
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357134"
---
# <a name="passwordless-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Inloggning med lösen ord med Microsoft Authenticator-appen (offentlig för hands version)

Microsoft Authenticator-appen kan användas för att logga in på ett Azure AD-konto utan att använda ett lösen ord. På samma sätt som med [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification), använder Microsoft Authenticator nyckelbaserad autentisering för att aktivera autentiseringsuppgifter som är knutna till en enhet och använder bio metrisk eller PIN-kod.

![Exempel på en webb läsar inloggning som ber användaren att godkänna inloggningen](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

I stället för att se en uppmaning om att ange ett lösen ord när du har angett ett användar namn, kommer en person som har aktiverat telefonin loggning i Microsoft Authenticator-appen att se ett meddelande som säger att de trycker på ett nummer i appen. Användaren måste matcha siffran i appen, välja Godkänn, ange sin PIN-kod eller bio metrisk, så slutförs autentiseringen.

## <a name="enable-my-users"></a>Aktivera mina användare

### <a name="tenant-prerequisites"></a>Krav för klient organisation

* Azure Active Directory
* Slutanvändare aktiverade för Azure Multi-Factor Authentication
* Användare kan registrera sina enheter

### <a name="steps-to-enable"></a>Steg för att aktivera

Följ stegen i artikeln [Aktivera lösen ords lös inloggning för Azure AD](howto-authentication-passwordless-enable.md#enable-new-passwordless-authentication-methods)för att aktivera metoder för lösen ords lös autentisering i din katalog.

> [!NOTE]
> Om du tidigare har aktiverat den här funktionen för din klient med hjälp av ett PowerShell-skript, skriver en ny princip för användare och grupper över den befintliga principen för hela klienten. 
>

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Hur aktiverar mina slutanvändare telefonin loggning?

För offentlig för hands version finns det inget sätt att tvinga användare att skapa eller använda den här nya autentiseringsuppgiften. En slutanvändare kommer bara att upptäcka lösen ords lös inloggning när en administratör har aktiverat sin klient och användaren har uppdaterat sin Microsoft Authenticator app för att aktivera telefonin loggning.

> [!NOTE]
> Den här funktionen har funnits i appen sedan mars 2017, så det finns en möjlighet att när principen är aktive rad för en klient, kan användarna stöta på det här flödet omedelbart. Tänk på och Förbered dina användare för den här ändringen.
>

1. Registrera dig för Azure Multi-Factor Authentication
1. Den senaste versionen av Microsoft Authenticator installerad på enheter som kör iOS 8,0 eller senare, eller Android 6,0 eller senare.
1. Arbets-eller skol konto med push-meddelanden tillagda i appen. Dokumentation om slutanvändare finns på [https://aka.ms/authappstart](https://aka.ms/authappstart).

När användaren har MFA-kontot med push-meddelanden som har kon figurer ATS i Microsoft Authenticator-appen, kan de följa stegen i artikeln [Logga in med din telefon, inte ditt lösen ord](../user-help/microsoft-authenticator-app-phone-signin-faq.md) för att slutföra telefon registreringen.

## <a name="known-issues"></a>Kända problem

### <a name="ad-fs-integration"></a>AD FS-integrering

När en användare har aktiverat Microsoft Authenticator lösen ords lös autentiseringsuppgifter kommer autentiseringen för användaren alltid att vara standard att skicka ett meddelande för godkännande. Den här logiken förhindrar att användare i en hybrid klient dirigeras till ADFS för inloggnings verifiering utan att användaren vidtar ytterligare steg för att klicka på Använd lösen ordet i stället. Den här processen kringgår också alla lokala principer för villkorlig åtkomst och genom strömnings flöden. Undantaget till den här processen är om en login_hint har angetts, en användare vidarebefordras automatiskt till AD FS och kringgå alternativet att använda lösen ords lös autentiseringsuppgifter.

### <a name="azure-mfa-server"></a>Azure MFA-Server

Slutanvändare som har Aktiver ATS för MFA via en organisations lokala Azure MFA-Server kan fortfarande skapa och använda en enda inloggnings inloggning för lösen ord. Om användaren försöker uppgradera flera installationer (5 +) av Microsoft Authenticator med autentiseringsuppgifterna kan denna ändring resultera i ett fel.  

### <a name="device-registration"></a>Enhetsregistrering

En av kraven för att skapa den nya, starka autentiseringsuppgiften är att enheten där den finns är registrerad i Azure AD-klienten, till en enskild användare. På grund av begränsningar för enhets registrering kan en enhet bara registreras i en enda klient. Den här gränsen innebär att endast ett arbets-eller skol konto i Microsoft Authenticator-appen kan aktive ras för telefonin loggning.

## <a name="next-steps"></a>Nästa steg

[Vad är lösen ord?](concept-authentication-passwordless.md)

[Läs mer om enhets registrering](../devices/overview.md#getting-devices-in-azure-ad)

[Lär dig mer om Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
