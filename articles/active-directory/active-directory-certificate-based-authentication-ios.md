---
title: Azure Active Directory certifikatbaserad autentisering på iOS
description: Mer information om scenarierna som stöds och kraven för att konfigurera certifikatbaserad autentisering i lösningar med iOS-enheter
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: 6b19d0556952224ba67914bfa74aac64ade2ea69
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory certifikatbaserad autentisering på iOS

iOS-enheter kan använda certifikatbaserad autentisering (CBA) för att autentisera till Azure Active Directory med ett klientcertifikat på sin enhet när du ansluter till:

* Mobila Office-program, till exempel Microsoft Outlook och Microsoft Word
* Exchange ActiveSync (EAS) klienter

Konfigurera den här funktionen eliminerar behovet av att ange en kombination av användarnamn och lösenord i vissa e-post och Microsoft Office-program på din mobila enhet.

Det här avsnittet innehåller kraven och scenarierna som stöds för att konfigurera CBA på en iOS(Android) enhet för användare av klienter i Office 365 Enterprise, företag, Education, som tillhör amerikanska myndigheter, Kina och Tyskland planer.

Den här funktionen är tillgängliga i förhandsversionen i Office 365 US Government skydd och federala planer.

## <a name="microsoft-mobile-applications-support"></a>Microsoft-supporten för mobila program

| Program | Support |
| --- | --- |
| Azure Information Protection-app |![Markera][1] |
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

En federationsserver konfigureras.

Microsoft Authenticator krävs för Office-program på iOS.

För Azure Active Directory att återkalla ett certifikat för har AD FS-token följande krav:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Serienumret för klientens certifikat)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Sträng för utfärdaren av klientcertifikatet)

Dessa anspråk till uppdateringstoken som infogas i Azure Active Directory om de är tillgängliga i AD FS-token (eller andra SAML-token). När uppdateringstoken som måste verifieras, används den här informationen för att kontrollera återkallningen.

Som bästa praxis bör du uppdatera felsidor för din organisations AD FS med följande information:

* Krav för att installera Microsoft Authenticator på iOS
* Anvisningar om hur du hämtar ett användarcertifikat.

Mer information finns i [anpassa AD FS Sign-in-sidor](https://technet.microsoft.com/library/dn280950.aspx).

Vissa Office-appar (med modern autentisering är aktiverat) skicka '*uppmana = inloggningen*' till Azure AD i sin begäran. Som standard översätter Azure AD '*uppmana = inloggningen*”i begäran till ADFS som”*wauth = usernamepassworduri*' (frågar AD FS för att göra U/P Auth) och '*wfresh = 0*' (frågar AD FS till Ignorera SSO tillstånd och gör en ny autentisering). Om du vill aktivera certifikatbaserad autentisering för dessa appar måste du ändra standardbeteendet för Azure AD. Anger den '*PromptLoginBehavior*”i federerade domäninställningarna till'*inaktiverad*'.
Du kan använda den [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) för att utföra den här uppgiften:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Stöd för Exchange ActiveSync-klienter

Intern iOS e-postklient stöds på iOS 9 eller senare. Kontakta din programutvecklaren för att avgöra om den här funktionen stöds för alla Exchange ActiveSync-program.

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera certifikatbaserad autentisering i din miljö, se [komma igång med certifikatbaserad autentisering på Android](active-directory-certificate-based-authentication-get-started.md) anvisningar.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
