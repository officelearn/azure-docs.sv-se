---
title: "Azure Active Directory certifikatbaserad autentisering på Android | Microsoft Docs"
description: "Mer information om scenarierna som stöds och kraven för att konfigurera certifikatbaserad autentisering i lösningar med Android-enheter"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: mtillman
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/13/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: d3e2ed44777a5963c8bd88f6479c4c58bebdb940
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Azure Active Directory certifikatbaserad autentisering på Android


Certifikatbaserad autentisering (CBA) gör att du ska kunna autentiseras av Azure Active Directory med ett klientcertifikat på en Windows-, Android eller iOS-enhet när du ansluter din Exchange-onlinekonto till:

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


### <a name="implementation-requirements"></a>Implementeringskrav för

Enhetens OS-version måste vara Android 5.0 (Lollipop) och senare.

En federationsserver konfigureras.  

För Azure Active Directory att återkalla ett certifikat för har AD FS-token följande krav:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (Serienumret för klientens certifikat)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (Sträng för utfärdaren av klientcertifikatet)

Dessa anspråk till uppdateringstoken som infogas i Azure Active Directory om de är tillgängliga i AD FS-token (eller andra SAML-token). När uppdateringstoken som måste verifieras, används den här informationen för att kontrollera återkallningen.

Som bästa praxis bör du uppdatera felsidor ADFS med instruktioner om hur du hämtar ett användarcertifikat.  
Mer information finns i [anpassa AD FS Sign-in-sidor](https://technet.microsoft.com/library/dn280950.aspx).  

Vissa Office-appar (med modern autentisering är aktiverat) skicka '*uppmana = inloggningen*' till Azure AD i sin begäran. Som standard översätter det Azure AD i begäran till ADFS till '*wauth = usernamepassworduri*' (frågar AD FS för att göra U/P auth) och '*wfresh = 0*' (frågar AD FS att ignorera SSO tillstånd och gör en ny autentisering). Om du vill aktivera certifikatbaserad autentisering för dessa appar måste du ändra standardbeteendet för Azure AD. Anger den '*PromptLoginBehavior*”i federerade domäninställningarna till'*inaktiverad*'.
Du kan använda den [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) för att utföra den här uppgiften:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`



## <a name="exchange-activesync-clients-support"></a>Stöd för Exchange ActiveSync-klienter
Vissa Exchange ActiveSync-program på Android 5.0 (Lollipop) eller senare stöds. Kontakta din programutvecklaren för att avgöra om e-postprogrammet stöder den här funktionen.


## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera certifikatbaserad autentisering i din miljö, se [komma igång med certifikatbaserad autentisering på Android](active-directory-certificate-based-authentication-get-started.md) anvisningar.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
