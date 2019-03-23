---
title: Certifikatbaserad autentisering på iOS - Azure Active Directory
description: Läs mer om scenarierna som stöds och kraven för att konfigurera certifikatbaserad autentisering i lösningar med iOS-enheter
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: cda1b1c2a484f3aa627b8b9cf486528d13f27be8
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369436"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory-certifikatbaserad autentisering på iOS

iOS-enheter kan använda certifikatbaserad autentisering (CBA) för att autentisera till Azure Active Directory med ett klientcertifikat på enheten när du ansluter till:

* Mobila Office-program, till exempel Microsoft Outlook och Microsoft Word
* Exchange ActiveSync (EAS) klienter

Konfigurerar den här funktionen eliminerar behovet av att ange en kombination av användarnamn och lösenord i vissa e-post och Microsoft Office-program på din mobila enhet.

Det här avsnittet innehåller kraven och scenarierna som stöds för att konfigurera CBA på en iOS(Android) enhet för användare av klienter i Office 365 Enterprise, företag, utbildning, US Government, Kina och Tyskland planer.

Den här funktionen är tillgänglig som förhandsversion i Office 365 US Government Defense och federala planer.

## <a name="microsoft-mobile-applications-support"></a>Microsoft-supporten för mobila program

| Program | Support |
| --- | --- |
| Azure Information Protection-appen |![Markera vilket tyder på stöd för det här programmet][1] |
| Intune-Företagsportalen |![Markera vilket tyder på stöd för det här programmet][1] |
| Microsoft Teams |![Markera vilket tyder på stöd för det här programmet][1] |
| OneNote |![Markera vilket tyder på stöd för det här programmet][1] |
| OneDrive |![Markera vilket tyder på stöd för det här programmet][1] |
| Outlook |![Markera vilket tyder på stöd för det här programmet][1] |
| Power BI |![Markera vilket tyder på stöd för det här programmet][1] |
| Skype för företag |![Markera vilket tyder på stöd för det här programmet][1] |
| Word / Excel / PowerPoint |![Markera vilket tyder på stöd för det här programmet][1] |
| Yammer |![Markera vilket tyder på stöd för det här programmet][1] |

## <a name="requirements"></a>Krav

Enhetens OS-version måste vara iOS 9 och senare

En federationsserver måste konfigureras.

Microsoft Authenticator krävs för Office-program på iOS.

AD FS-token måste ha följande anspråk för Azure Active Directory att återkalla ett klientcertifikat:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Serienummer för klientcertifikatet)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Sträng för utfärdaren av klientcertifikatet)

Dessa anspråk till uppdateringstoken infogas i Azure Active Directory om de är tillgängliga i AD FS-token (eller andra SAML-token). När uppdateringstoken måste verifieras, används den här informationen till den återkallningskontroll.

Som bästa praxis bör du uppdatera din organisations AD FS-felsidor med följande information:

* Krav för att installera Microsoft Authenticator på iOS
* Anvisningar för hur du hämtar ett användarcertifikat.

Mer information finns i [anpassa sidor AD FS Sign-in](https://technet.microsoft.com/library/dn280950.aspx).

Vissa Office-appar (med modern autentisering är aktiverat) skicka ”*kommandoprompt = login*' till Azure AD i begäran. Som standard översätter Azure AD ”*kommandoprompt = login*'i begäran till AD FS som”*wauth = usernamepassworduri*”(ber ADFS göra U/P Auth) och”*wfresh = 0*' (ber ADFS till Ignorera SSO tillstånd och gör en ny autentisering). Om du vill aktivera certifikatbaserad autentisering för dessa appar måste du ändra standardbeteendet för Azure AD. Ange den '*PromptLoginBehavior*”i inställningarna för din federerad domän till'*inaktiverad*'.
Du kan använda den [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) cmdlet för att utföra den här uppgiften:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Stöd för Exchange ActiveSync-klienter

Den interna iOS e-postklienten stöds på iOS 9 eller senare. Kontakta din programutvecklare för att avgöra om den här funktionen stöds för alla Exchange ActiveSync-program.

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera certifikatbaserad autentisering i din miljö finns i [Kom igång med certifikatbaserad autentisering på Android](../authentication/active-directory-certificate-based-authentication-get-started.md) anvisningar.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
