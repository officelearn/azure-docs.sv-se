---
title: Certifikatbaserad autentisering på iOS - Azure Active Directory
description: Lär dig mer om scenarier som stöds och kraven för att konfigurera certifikatbaserad autentisering för Azure Active Directory i lösningar med iOS-enheter
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639634"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory-certifikatbaserad autentisering på iOS

För att förbättra säkerheten kan iOS-enheter använda certifikatbaserad autentisering (CBA) för att autentisera till Azure Active Directory (Azure AD) med hjälp av ett klientcertifikat på sin enhet när de ansluter till följande program eller tjänster:

* Office-mobilappar som Microsoft Outlook och Microsoft Word
* Exchange ActiveSync-klienter (EAS)

Genom att använda certifikat elimineras behovet av att ange en kombination av användarnamn och lösenord i vissa e-post- och Microsoft Office-program på din mobila enhet.

I den här artikeln beskrivs kraven och scenarierna som stöds för att konfigurera CBA på en iOS-enhet. CBA för iOS är tillgängligt i Offentliga Azure-moln, Microsoft Government Cloud, Microsoft Cloud Germany och Microsoft Azure China 21Vianet.

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

För att använda CBA med iOS gäller följande krav och överväganden:

* Enhetens OS-version måste vara iOS 9 eller högre.
* Microsoft Authenticator krävs för Office-program på iOS.
* En identitetsinställning måste skapas i macOS-nyckelringen som innehåller ADFS-serverns autentiserings-URL. Mer information finns [i Skapa en identitetsinställning i Nyckelringsåtkomst på Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Följande ADFS-krav och överväganden (Active Directory Federation Services) gäller:

* ADFS-servern måste vara aktiverad för certifikatautentisering och användning av federerad autentisering.
* Certifikatet måste använda EKU (Enhanced Key Usage) och innehålla ANVÄNDARENs UPN i *ämnesalternativnamnet (NT Principal Name)*.

## <a name="configure-adfs"></a>Konfigurera ADFS

För att Azure AD ska kunna återkalla ett klientcertifikat måste ADFS-token ha följande anspråk. Azure AD lägger till dessa anspråk i uppdateringstoken om de är tillgängliga i ADFS-token (eller någon annan SAML-token). När uppdateringstoken måste valideras används den här informationen för att kontrollera återkallandet:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- lägg till serienumret för ditt klientcertifikat
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- lägga till strängen för utfärdaren av ditt klientcertifikat

Du bör också uppdatera organisationens ADFS-felsidor med följande information:

* Kravet för att installera Microsoft Authenticator på iOS.
* Instruktioner om hur du skaffar ett användarcertifikat.

Mer information finns [i Anpassa AD FS-inloggningssidan](https://technet.microsoft.com/library/dn280950.aspx).

## <a name="use-modern-authentication-with-office-apps"></a>Använda modern autentisering med Office-appar

Vissa Office-appar med modern `prompt=login` autentisering aktiverad skicka till Azure AD i sin begäran. Som standard översätter `prompt=login` Azure AD i begäran `wauth=usernamepassworduri` till ADFS som (ber ADFS `wfresh=0` att göra U/P Auth) och (ber ADFS att ignorera SSO-tillstånd och göra en ny autentisering). Om du vill aktivera certifikatbaserad autentisering för dessa appar ändrar du standardbeteendet för Azure AD.

Om du vill uppdatera standardbeteendet anger du *' PromptLoginBehavior*' i dina federerade domäninställningar till *Inaktiverat*. Du kan använda cmdleten [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) för att utföra den här uppgiften, som visas i följande exempel:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Stöd för Exchange ActiveSync-klienter

På iOS 9 eller senare stöds den inbyggda iOS-e-postklienten. Om du vill ta reda på om den här funktionen stöds för alla andra Exchange ActiveSync-program kontaktar du programutvecklaren.

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera certifikatbaserad autentisering i din miljö läser [du Komma igång med certifikatbaserad autentisering](active-directory-certificate-based-authentication-get-started.md) för instruktioner.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
