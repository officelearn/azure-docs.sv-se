---
title: Azure Active Directory-certifikatbaserad autentisering på iOS
description: Läs mer om scenarierna som stöds och kraven för att konfigurera certifikatbaserad autentisering i lösningar med iOS-enheter
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: 655fa6b4bf0f04f2d88e9a3f11cb9d3917ea3dd3
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345101"
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
| Azure Information Protection-appen |![Markera][1] |
| Intune-Företagsportalen |![Markera][1] |
| Microsoft Teams |![Markera][1] |
| OneNote |![Markera][1] |
| OneDrive |![Markera][1] |
| Outlook |![Markera][1] |
| Power BI |![Markera][1] |
| Skype för företag |![Markera][1] |
| Word / Excel / PowerPoint |![Markera][1] |
| Yammer |![Markera][1] |

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
