---
title: Certifikatbaserad autentisering på iOS - Azure Active Directory
description: Lär dig mer om scenarier som stöds och kraven för att konfigurera certifikatbaserad autentisering i lösningar med iOS-enheter
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a042897ecbe35935c1832a53f523eb0597ebafc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654248"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory-certifikatbaserad autentisering på iOS

iOS-enheter kan använda certifikatbaserad autentisering (CBA) för att autentisera till Azure Active Directory med hjälp av ett klientcertifikat på sin enhet när de ansluter till:

* Office-mobilappar som Microsoft Outlook och Microsoft Word
* Exchange ActiveSync-klienter (EAS)

Om du konfigurerar den här funktionen elimineras behovet av att ange en kombination av användarnamn och lösenord i vissa e-post- och Microsoft Office-program på din mobila enhet.

Det här avsnittet innehåller krav och scenarier som stöds för att konfigurera CBA på en iOS(Android)-enhet för användare av klienter i Office 365 Enterprise, Business, Education, US Government, Kina och Tyskland.

Den här funktionen är tillgänglig i förhandsversionen i Office 365 US Government Defense och Federal plans.

## <a name="microsoft-mobile-applications-support"></a>Stöd för Microsoft-mobilappar

| Program | Support |
| --- | --- |
| Azure-appen för informationsskydd |![Markera markera som markerar service för denna applikation][1] |
| Intune Företagsportal |![Markera markera som markerar service för denna applikation][1] |
| Microsoft Teams |![Markera markera som markerar service för denna applikation][1] |
| OneNote |![Markera markera som markerar service för denna applikation][1] |
| OneDrive |![Markera markera som markerar service för denna applikation][1] |
| Outlook |![Markera markera som markerar service för denna applikation][1] |
| Power BI |![Markera markera som markerar service för denna applikation][1] |
| Skype för företag |![Markera markera som markerar service för denna applikation][1] |
| Word / Excel / PowerPoint |![Markera markera som markerar service för denna applikation][1] |
| Yammer |![Markera markera som markerar service för denna applikation][1] |

## <a name="requirements"></a>Krav

Enheten OS-versionen måste vara iOS 9 och högre

En federationsserver måste konfigureras.

Microsoft Authenticator krävs för Office-program på iOS.

För att Azure Active Directory ska kunna återkalla ett klientcertifikat måste ADFS-token ha följande anspråk:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(Serienumret för klientcertifikatet)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(Strängen för utfärdaren av klientcertifikatet)

Azure Active Directory lägger till dessa anspråk i uppdateringstoken om de är tillgängliga i ADFS-token (eller någon annan SAML-token). När uppdateringstoken måste valideras används den här informationen för att kontrollera återkallelsen.

Du bör också uppdatera organisationens ADFS-felsidor med följande information:

* Kravet för att installera Microsoft Authenticator på iOS
* Instruktioner om hur du skaffar ett användarcertifikat.

Mer information finns [i Anpassa AD FS-inloggningssidorna](https://technet.microsoft.com/library/dn280950.aspx).

Vissa Office-appar (med modern autentisering aktiverad) skickar*snabb=inloggning*till Azure AD i sin begäran. Som standard översätter Azure AD '*prompt=login*' i begäran till ADFS som '*wauth=usernamepassworduri*' (ber ADFS att göra U/P Auth) och '*wfresh=0*' (ber ADFS att ignorera SSO-tillstånd och göra en ny autentisering). Om du vill aktivera certifikatbaserad autentisering för dessa appar måste du ändra standardbeteendet för Azure AD. Ställ bara in "*PromptLoginBehavior*' i dina federerade domäninställningar på "*Inaktiverad*".
Du kan använda cmdleten [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) för att utföra den här uppgiften:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Support för Exchange ActiveSync-klienter

På iOS 9 eller senare stöds den inbyggda iOS-e-postklienten. Kontakta programutvecklaren för alla andra Exchange ActiveSync-program för att avgöra om den här funktionen stöds.

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera certifikatbaserad autentisering i din miljö läser [du Komma igång med certifikatbaserad autentisering på Android](../authentication/active-directory-certificate-based-authentication-get-started.md) för instruktioner.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
