---
title: Villkorlig åtkomst för B2B-samarbetsanvändare – Azure AD
description: Azure Active Directory B2B-samarbete stöder MFA (Multi Factor Authentication) för selektiv åtkomst till dina företagsprogram
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0b6ceba4c3c9202e2024b5c163c0e98bb6cbf55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273003"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Villkorlig åtkomst för B2B-samarbetsanvändare

## <a name="multi-factor-authentication-for-b2b-users"></a>Multifaktorautentisering för B2B-användare
Med Azure AD B2B-samarbete kan organisationer tillämpa MFA-principer (Multi Factor Authentication) för B2B-användare. Dessa principer kan tillämpas på klient-, app- eller individanvändarnivå, på samma sätt som de är aktiverade för heltidsanställda och medlemmar i organisationen. MFA-principer tillämpas på resursorganisationen.

Exempel:
1. Admin eller informationsarbetare i företag A bjuder in användare från företag B till ett program *Foo* i företag A.
2. Program *Foo* i företag A är konfigurerad för att kräva MFA vid åtkomst.
3. När användaren från företag B försöker komma åt appen *Foo* i företaget En klientorganisation ombeds de att slutföra en MFA-utmaning.
4. Användaren kan ställa in sin MFA med företag A och väljer sitt MFA-alternativ.
5. Det här scenariot fungerar för alla identiteter (Azure AD eller MSA, till exempel om användare i företag B autentiserar med hjälp av socialt ID)
6. Företag A måste ha tillräckliga Premium Azure AD-licenser som stöder MFA. Användaren från företag B använder den här licensen från företag A.

Den inbjudande hyresrätten är alltid ansvarig för MFA för användare från partnerorganisationen, även om partnerorganisationen har MFA-funktioner.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Ställa in MFA för B2B-samarbetsanvändare
För att upptäcka hur enkelt det är att konfigurera MFA för B2B-samarbetsanvändare, se hur i följande video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B användare MFA erfarenhet för erbjudande inlösen
Kolla in följande animering för att se inlösenupplevelsen:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA-återställning för B2B-samarbetsanvändare
För närvarande kan administratören kräva B2B-samarbetsanvändare att korrekturse upp igen endast med hjälp av följande PowerShell-cmdlets:

1. Anslut till Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Få alla användare med korrektur upp metoder

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Här är ett exempel:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Återställ MFA-metoden för en viss användare så att den kräver att B2B-samarbetsanvändaren ställer in korrekturmetoder igen. Exempel:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Varför utför vi MFA på resurs hyresrätt?

I den aktuella versionen är MFA alltid i resursen hyresrätt, på grund av förutsägbarhet. Anta till exempel att en Contoso-användare (Sally) är inbjuden till Fabrikam och Fabrikam har aktiverat MFA för B2B-användare.

Om Contoso har MFA-principen aktiverad för App1 men inte App2 kan vi se följande problem om vi tittar på Contoso MFA-anspråket i token:

* Dag 1: En användare har MFA i Contoso och använder App1, då visas ingen ytterligare MFA-prompt i Fabrikam.

* Dag 2: Användaren har åtkomst till App 2 i Contoso, så nu när de öppnar Fabrikam måste de registrera sig för MFA där.

Den här processen kan vara förvirrande och kan leda till att inloggningen avslutas.

Dessutom, även om Contoso har MFA-kapacitet, är det inte alltid så att Fabrikam skulle lita på Contoso MFA-principen.

Slutligen fungerar resursklientens MFA också för MSA:er och sociala ID:er och för partnerorganisationar som inte har MFA-uppsättning.

Därför är rekommendationen för MFA för B2B-användare att alltid kräva MFA i den inbjudande klienten. Detta krav kan leda till dubbla MFA i vissa fall, men när du öppnar den inbjudande klienten är slutanvändarens upplevelse förutsägbar: Sally måste registrera sig för MFA med den inbjudande klienten.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Enhetsbaserad, platsbaserad och riskbaserad villkorlig åtkomst för B2B-användare

När Contoso aktiverar enhetsbaserade principer för villkorlig åtkomst för sina företagsdata förhindras åtkomst från enheter som inte hanteras av Contoso och som inte är kompatibla med Contoso-enhetsprinciperna.

Om B2B-användarens enhet inte hanteras av Contoso blockeras åtkomsten för B2B-användare från partnerorganisationerna i oavsett sammanhang som dessa principer tillämpas. Contoso kan dock skapa undantagslistor som innehåller specifika partneranvändare för att utesluta dem från den enhetsbaserade principen för villkorlig åtkomst.

#### <a name="mobile-application-management-policies-for-b2b"></a>Principer för hantering av mobilappar för B2B

Det går inte att tillämpa appskyddsprinciper för villkorlig åtkomst på B2B-användare eftersom den inbjudande organisationen inte har någon insyn i B2B-användarens hemorganisation.

#### <a name="location-based-conditional-access-for-b2b"></a>Platsbaserad villkorlig åtkomst för B2B

Platsbaserade principer för villkorlig åtkomst kan tillämpas för B2B-användare om den inbjudande organisationen kan skapa ett betrott IP-adressintervall som definierar deras partnerorganisationer.

#### <a name="risk-based-conditional-access-for-b2b"></a>Riskbaserad villkorlig åtkomst för B2B

För närvarande kan riskbaserade inloggningsprinciper inte tillämpas på B2B-användare eftersom riskbedömningen utförs på B2B-användarens hemorganisation.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Azure AD B2B-samarbetslicensiering](licensing-guidance.md)
* [Vanliga frågor och svar om Azure Active Directory B2B-samarbete](faq.md)
