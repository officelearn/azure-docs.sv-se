---
title: Lösenord utan Azure AD-inloggningen med Microsoft Authenticator-appen (offentlig förhandsversion)
description: Logga in på Azure AD med Microsoft Authenticator-appen utan att använda lösenordet (offentlig förhandsversion)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: librown
ms.openlocfilehash: b09bb65cdb571c9df95d1922f4132abe5b77907c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963955"
---
# <a name="password-less-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Lösenord utan telefoninloggning med Microsoft Authenticator-appen (offentlig förhandsversion)

Microsoft Authenticator-appen kan användas för att logga in på alla Azure AD-konto utan lösenord. Liknande teknik för [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator använder nyckel-baserad autentisering för att aktivera en autentiseringsuppgift för användare som är kopplad till en enhet och använder en biometriska eller PIN-kod.

![Exempel på en webbläsare inloggning där personen ombeds användaren att godkänna försök logga in i sina Microsoft Authenticator-appen](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

I stället för ett oväntat meddelande för ett lösenord när du har angett ett användarnamn, visas en person som har aktiverat telefoninloggning i Microsoft Authenticator-appen ett meddelande som uppmanar dem att trycka på ett nummer i appen. I appen, användaren måste stämma med numret, väljer du Godkänn och sedan ange sin PIN-kod eller biometriska sedan autentiseringen slutförs.

## <a name="enable-my-users"></a>Aktivera Mina användare

För en förhandsversion, en administratör måste först lägga till en princip via powershell som tillåter användning av autentiseringsuppgifter i klienten. Läs avsnittet ”kända problem” innan du tar det här steget.

### <a name="tenant-prerequisites"></a>Krav för klient

* Azure Active Directory
* Slutanvändare som har aktiverats för Azure Multi-Factor Authentication
* Användare kan registrera sina enheter

### <a name="steps-to-enable"></a>Steg för att aktivera

Kontrollera att du har den senaste versionen av den offentliga förhandsversionen av Azure Active Directory V2 PowerShell-modulen. Du kanske vill avinstallera och installera om för att kontrollera detta genom att köra följande kommandon:

1. `Uninstall-Module -Name AzureADPreview`
2. `Install-Module -Name AzureADPreview`

Du kan aktivera den lösenord utan inloggning förhandsvisning mobil med hjälp av följande PowerShell-kommandon:

1. `Connect-AzureAD`
   1. Logga in med ett konto i klient i dialogrutan för autentisering. Kontot måste antingen vara en säkerhetsadministratör eller Global administratör.
1. `New-AzureADPolicy -Type AuthenticatorAppSignInPolicy -Definition '{"AuthenticatorAppSignInPolicy":{"Enabled":true}}' -isOrganizationDefault $true -DisplayName AuthenticatorAppSignIn`

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Hur aktiverar slutanvändarna telefoninloggning?

För en förhandsversion går det inte att tvinga användare att skapa eller använda den här nya autentiseringsuppgifter. En slutanvändare inträffar bara logga in utan lösenord när en administratör har aktiverat sin klient och användaren har uppdaterat sina Microsoft Authenticator-appen om du vill aktivera telefoninloggning.

> [!NOTE]
> Den här funktionen har varit i appen sedan mars 2017, så det finns en möjlighet att när principen är aktiverad för en klient, kan användarna få det här flödet omedelbart. Tänk på och Förbered dina användare för den här ändringen.
>

1. Registrera dig i Azure Multi-Factor Authentication
1. Senaste versionen av Microsoft Authenticator installerade på enheter som kör iOS 8.0 eller senare eller Android 6.0 eller senare.
1. Arbets- eller skolkonto konto med push-meddelanden som har lagts till i appen. Slutanvändardokumentation finns på [ https://aka.ms/authappstart ](https://aka.ms/authappstart).

När användaren har MFA-konto med push-meddelanden som ställts in i Microsoft Authenticator-appen, kan de följer du stegen i artikeln [logga in med din telefon, inte ditt lösenord](../user-help/microsoft-authenticator-app-phone-signin-faq.md) att slutföra registreringen för phone logga in.

## <a name="known-issues"></a>Kända problem

### <a name="ad-fs-integration"></a>AD FS-integrering

När en användare har aktiverat Microsoft Authenticator lösenord utan autentiseringsuppgifter, autentisering för den användaren alltid som standard skickar ett meddelande om godkännande. Den här logiken som förhindrar att användare i en hybrid-klient dirigeras till AD FS för att bekräfta din inloggning utan att användaren tar ytterligare ett steg att klicka på ”Använd ditt lösenord istället”. Den här processen kommer också kringgå alla principer för villkorlig åtkomst av lokala och direkt autentiseringsflöden. Undantag till den här processen är om en login_hint är anges en användare vara automatiskt till AD FS och kringgå alternativet att använda lösenord utan autentiseringsuppgifter.

### <a name="azure-mfa-server"></a>Azure MFA-servern

Slutanvändare som är aktiverade för MFA via en organisations lokal Azure MFA server kan fortfarande skapa och använda autentiseringsuppgift för enkel lösenord utan telefonnummer. Om du försöker uppgradera flera installationer (5 +) av Microsoft Authenticator med autentiseringsuppgifterna, kan den här ändringen resultera i ett fel.  

### <a name="device-registration"></a>Enhetsregistrering

En av kraven för att skapa den här nya, starka autentiseringsuppgifter är att enheten där det finns är registrerad i Azure AD-klient till en enskild användare. På grund av begränsningar för registrering av enheter kan endast en enhet registreras i en enda klient. Den här gränsen innebär att endast ett arbets- eller skolkonto konto i Microsoft Authenticator-appen kan aktiveras för telefoninloggning.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om enhetsregistrering](../devices/overview.md#getting-devices-under-the-control-of-azure-ad)

[Lär dig mer om Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
