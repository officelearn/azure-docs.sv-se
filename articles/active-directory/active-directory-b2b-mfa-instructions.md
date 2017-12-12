---
title: "Villkorlig åtkomst för användare i Azure Active Directory B2B-samarbete | Microsoft Docs"
description: "Azure Active Directory B2B-samarbete stöder multifaktorautentisering (MFA) för selektiv åtkomst till företagets program"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 09/11/2017
ms.author: sasubram
ms.openlocfilehash: 2f2cfc351d372d665aac054d52d6e1520e1ffe48
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Villkorlig åtkomst för användare för B2B-samarbete

## <a name="multi-factor-authentication-for-b2b-users"></a>Multi-Factor authentication för B2B-användare
Organisationer kan tillämpa principer för multifaktorautentisering (MFA) för B2B-användare med Azure AD B2B-samarbete. Dessa principer kan tillämpas på klienten, app eller enskilda användarnivå, på samma sätt som de är aktiverade för heltidsanställda och medlemmar i organisationen. MFA-principer tillämpas på resursorganisationen.

Exempel:
1. Administratören eller information worker i företag A uppmanar användare från företaget B till ett program *Foo* i företag A.
2. Programmet *Foo* i företag A är konfigurerad för att kräva MFA på åtkomst.
3. När användare från företaget B försöker komma åt appen *Foo* i företaget en klient villkoren uppmanas de att slutföra MFA-kontrollen.
4. Användaren kan ställa in sina MFA med företagets A och väljer alternativet för Multifaktorautentisering.
5. Det här scenariot fungerar för alla identitet (Azure AD eller MSA om användare i företaget B autentisera med sociala ID exempelvis)
6. Företag A måste ha tillräckligt många licenser för Premium Azure AD som stöder MFA. Användare från företaget B använder denna licens från företag A.

Bjuda in innehavare är alltid ansvarig för MFA för användare från partnerorganisationen, även om partnerorganisationens har funktioner för MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Konfigurera MFA för B2B-samarbete användare
För att identifiera hur lätt det är att konfigurera MFA för B2B-samarbete användare finns i följande video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B användare MFA upplevelse i erbjuder inlösning
Kolla in följande animeringen ska se betalda upplevelse:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA återställa för B2B-samarbete användare
För närvarande kan kan administratören kräva B2B-samarbete användare bevis upp igen endast med hjälp av följande PowerShell-cmdlets:

1. Anslut till Azure AD

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Hämta alla användare med bevis metoder

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Här är ett exempel:

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Återställ MFA-metoden för en viss användare B2B-samarbete användaren ange bevis upp igen. Exempel:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Varför gör vi MFA på resursen innehavare?

I den aktuella versionen är MFA alltid i resurs-innehavare, på grund av förutsägbarhet. Anta att till exempel Fabrikam uppmanas användaren Contoso (obegränsad) och Fabrikam har aktiverat MFA för B2B-användare.

Om Contoso har aktiverats för App1 men inte App2 MFA-principen, kan sedan om vi tittar på Contoso MFA-anspråk i token, vi se följande problem:

* Dag 1: En användare har MFA i Contoso och har åtkomst till App1 och inga ytterligare MFA uppmaning visas i Fabrikam.

* Dag 2: Användaren har åtkomst till appen 2 i Contoso, så nu att få åtkomst till Fabrikam, de måste registrera sig för MFA det.

Den här processen kan vara förvirrande och kan leda till att släppa inloggningen slutföranden.

Dessutom även om Contoso MFA funktionen, är det inte alltid fallet Fabrikam skulle förtroende Contoso MFA-principen.

Slutligen fungerar resurs klient MFA även för MSA: er och sociala-ID: N och partner organisationer som behöver inte konfigurera MFA.

Rekommendation för MFA för B2B-användare är därför att alltid kräva MFA i bjuda in klienten. Detta krav kan leda till dubbla MFA i vissa fall, men när åtkomst till bjuda in innehavaren av slutanvändare är förutsägbar: Lisa måste registrera sig för MFA med bjuda in innehavaren.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Enhetsbaserad platsbaserad och risker-baserad villkorlig åtkomst för B2B-användare

När Contoso aktiverar enhetsbaserad villkorlig åtkomstvillkorspolicy till företagets data, förhindrade åtkomst från enheter som inte hanteras av Contoso och som inte är kompatibla med principer för Contoso-enheter.

Om enheten B2B inte hanteras av Contoso, blockeras åtkomsten B2B användare från resurspartner-organisationer i den kontexten principerna tillämpas. Contoso kan dock skapa undantagslistor som innehåller viss partner användare om du vill utelämna dem från principen för enhetsbaserad villkorlig åtkomst.

#### <a name="location-based-conditional-access-for-b2b"></a>Plats-baserad villkorlig åtkomst för B2B

Principer för plats-baserad villkorlig åtkomst tillämpas för B2B användare om bjuda in organisationen kan skapa en tillförlitlig IP-adressintervall som definierar deras partnerorganisationer.

#### <a name="risk-based-conditional-access-for-b2b"></a>Risk-baserad villkorlig åtkomst för B2B

För närvarande kan inte använda risk-baserade inloggning principer på B2B användare eftersom riskbedömningen utförs på B2B användarens hemorganisation.

## <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hur lägger Azure Active Directory-administratörer till B2B-samarbete användare?](active-directory-b2b-admin-add-users.md)
* [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
* [Elementen i e-postinbjudan B2B-samarbete](active-directory-b2b-invitation-email.md)
* [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B-samarbete och licensiering](active-directory-b2b-licensing.md)
* [Felsökning av Azure Active Directory B2B-samarbete](active-directory-b2b-troubleshooting.md)
* [Vanliga och frågor svar om Azure Active Directory B2B-samarbete](active-directory-b2b-faq.md)
* [Azure Active Directory B2B-samarbete API och anpassning](active-directory-b2b-api.md)
* [Lägg till B2B-samarbete användare utan inbjudan](active-directory-b2b-add-user-without-invite.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
