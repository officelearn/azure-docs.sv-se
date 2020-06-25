---
title: 'Azure AD Connect: sömlös enkel inloggning | Microsoft Docs'
description: I det här avsnittet beskrivs Azure Active Directory (Azure AD) sömlös enkel inloggning och hur du kan tillhandahålla verklig enkel inloggning för företagets Skriv bords användare i företags nätverket.
services: active-directory
keywords: Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: caf7db5f27ed6f612d0896bff0899feda3311883
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85357757"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory sömlös enkel inloggning

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Vad är Azure Active Directory sömlös enkel inloggning?

Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. När den är aktive rad behöver användarna inte ange sina lösen ord för att logga in på Azure AD, och vanligt vis skriver du i sina användar namn. Den här funktionen ger användarna enkel åtkomst till dina molnbaserade program utan några ytterligare lokala komponenter.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Sömlös SSO kan kombineras med antingen [lösen ordets hash-synkronisering](how-to-connect-password-hash-synchronization.md) eller inloggnings metoder för [direktautentisering](how-to-connect-pta.md) . Sömlös SSO kan _inte_ användas för Active Directory Federation Services (AD FS) (ADFS).

![Sömlös enkel inloggning](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Sömlös SSO behöver användarens **enhet enbart vara domänansluten,** men den används inte på [Azure AD-anslutna](../devices/concept-azure-ad-join.md) eller [hybrid Azure AD-anslutna](../devices/concept-azure-ad-join-hybrid.md) enheter. SSO på Azure AD-ansluten, hybrid Azure AD-ansluten och registrerade Azure AD-enheter fungerar baserat på den [primära uppdateringstoken](../devices/concept-primary-refresh-token.md).

## <a name="key-benefits"></a>Viktiga fördelar

- *En utmärkt användarupplevelse*
  - Användare loggas automatiskt in i både lokala och molnbaserade program.
  - Användarna behöver inte ange sina lösen ord flera gånger.
- *Enkelt att distribuera & administrera*
  - Inga ytterligare komponenter behövs lokalt för att det ska fungera.
  - Fungerar med vilken metod som helst av molnbaserad autentisering – [hash-synkronisering av lösen ord](how-to-connect-password-hash-synchronization.md) eller [direktautentisering](how-to-connect-pta.md).
  - Kan distribueras till några eller alla användare med hjälp av grupprincip.
  - Registrera icke-Windows 10-enheter med Azure AD utan att behöva AD FS infrastruktur. Den här funktionen kräver att du använder version 2,1 eller senare av [arbets platsen – ansluta till klienten](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Funktions markeringar

- Inloggnings användar namnet kan antingen vara det lokala standard användar namnet ( `userPrincipalName` ) eller ett annat attribut som kon figurer ATS i Azure AD Connect ( `Alternate ID` ). Båda användnings fallen fungerar eftersom sömlös SSO använder `securityIdentifier` anspråk i Kerberos-biljetten för att leta upp motsvarande användar objekt i Azure AD.
- Sömlös SSO är en Opportunistic-funktion. Om det Miss lyckas av någon anledning återgår användar inloggningen till sitt normala beteende, d.v.s. användaren måste ange sitt lösen ord på inloggnings sidan.
- Om ett program (till exempel `https://myapps.microsoft.com/contoso.com` ) vidarebefordrar en `domain_hint` (OpenID Connect) eller `whr` (SAML) parameter som identifierar din klient, eller `login_hint` parameter som identifierar användaren, i sin inloggnings förfrågan för Azure AD, loggas användarna automatiskt in utan att de anger användar namn eller lösen ord.
- Användarna får också en tyst inloggning om ett program (t. ex. `https://contoso.sharepoint.com` ) skickar inloggnings förfrågningar till Azure Ads slut punkter som har kon figurer ATS som klienter, det vill säga `https://login.microsoftonline.com/contoso.com/<..>` eller `https://login.microsoftonline.com/<tenant_ID>/<..>` – i stället för Azure Ads vanliga slut punkt – det vill säga `https://login.microsoftonline.com/common/<...>` .
- Utloggning stöds. Detta gör att användarna kan välja ett annat Azure AD-konto för att logga in med, i stället för att automatiskt logga in med sömlös SSO automatiskt.
- Office 365 Win32-klienter (Outlook, Word, Excel och andra) med versioner 16.0.8730. xxxx och senare stöds med ett icke-interaktivt flöde. För OneDrive måste du aktivera [funktionen OneDrive-tyst konfiguration](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) för att få en tyst inloggning.
- Den kan aktive ras via Azure AD Connect.
- Det är en kostnads fri funktion, och du behöver inte några betalda versioner av Azure AD för att använda den.
- Det finns stöd för webb läsar klienter och Office-klienter som stöder [modern autentisering](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) på plattformar och webbläsare som stöder Kerberos-autentisering:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ja\*|Ja|Ja|Ja\*\*\*|Ej tillämpligt
|Windows 8,1|Ja\*|Ej tillämpligt|Yes|Ja\*\*\*|Ej tillämpligt
|Windows 8|Ja\*|Ej tillämpligt|Yes|Ja\*\*\*|Ej tillämpligt
|Windows 7|Ja\*|Ej tillämpligt|Yes|Ja\*\*\*|Ej tillämpligt
|Windows Server 2012 R2 eller senare|Ja\*\*|Ej tillämpligt|Yes|Ja\*\*\*|Ej tillämpligt
|Mac OS X|Ej tillämpligt|Ej tillämpligt|Ja\*\*\*|Ja\*\*\*|Ja\*\*\*


\*Kräver Internet Explorer version 10 eller senare

\*\*Kräver Internet Explorer version 10 eller senare. Inaktivera utökat kernelläge

\*\*\*Kräver [ytterligare konfiguration](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>För Windows 10 rekommenderar vi att du använder [Azure AD Join](../devices/concept-azure-ad-join.md) för att få en optimal enkel inloggning med Azure AD.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart**](how-to-connect-sso-quick-start.md) – kom igång med Azure AD sömlös SSO.
- [**Distributions plan**](https://aka.ms/deploymentplans/sso) – steg-för-steg-distributions plan.
- [**Teknisk djupgående**](how-to-connect-sso-how-it-works.md) – förstå hur den här funktionen fungerar.
- [**Vanliga frågor**](how-to-connect-sso-faq.md) och svar på vanliga frågor.
- [**Felsök**](tshoot-connect-sso.md) – lär dig hur du löser vanliga problem med funktionen.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att arkivera nya funktions begär Anden.

