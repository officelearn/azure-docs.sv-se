---
title: Villkorlig åtkomst för B2B-samarbete användare – Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-samarbete stöder multifaktorautentisering (MFA) för selektiv åtkomst till företagets program
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5234443e234d232a9711274bea2f73427266f6e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113494"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Villkorlig åtkomst för användare i B2B-samarbetet

## <a name="multi-factor-authentication-for-b2b-users"></a>Multi-Factor authentication för B2B-användare
Organisationer kan tillämpa principer för multifaktorautentisering (MFA) för B2B-användare med Azure AD B2B-samarbete. Dessa principer kan tillämpas på innehavarens, app eller enskilda användarnivå, på samma sätt som de är aktiverade för heltidsanställda och medlemmar i organisationen. MFA-principer tillämpas i resursorganisationen.

Exempel:
1. Administratören eller information arbete i företag A bjuder in användare från företaget B till ett program *Foo* i företag A.
2. Programmet *Foo* i företag A är konfigurerad för att kräva MFA för åtkomst.
3. När användare från företaget B försöker få åtkomst till appen *Foo* på företaget en klient, uppmanas de att slutföra en MFA-kontrollen.
4. Användaren kan ställa in sina MFA med företag A och väljer sina MFA-alternativet.
5. Det här scenariot fungerar för alla identitet (Azure AD eller MSA, till exempel, om användare i företaget B autentisera med sociala ID)
6. Företag A måste ha tillräckligt många licenser för Premium Azure AD som stöder MFA. Användaren från företagets B använder den här licens från företag A.

Bjuder in innehavare är alltid ansvarig för MFA för användare från partnerorganisationen, även om partnerorganisationen har funktioner för MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Konfigurera MFA för användare i B2B-samarbetet
För att identifiera hur enkelt det är att konfigurera MFA för användare i B2B-samarbetet, se hur det fungerar i följande video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B-användare MFA upplevelse för erbjuder inlösen
Kolla in följande animeringen ska se återköp upplevelse:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA Självbetjäning för användare i B2B-samarbetet
För närvarande kan kan administratören kräva användare till korrektur i B2B-samarbetet upp igen endast med hjälp av följande PowerShell-cmdletar:

1. Anslut till Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Hämta alla användare med bevis upp metoder

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Här är ett exempel:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Återställ MFA-metoden för en specifik användare så att B2B-samarbete användaren att ange bevis och upp igen. Exempel:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Varför utför vi MFA på resurs-innehavare?

I den aktuella versionen är MFA alltid i resurs-innehavare, på grund av att förutse. Exempel: Anta en Contoso-användare (Lisa) bjuds in till Fabrikam och Fabrikam har aktiverat MFA för B2B-användare.

Om Contoso har MFA principen är aktiverad för App1 men inte App2, kan sedan om vi tittar på Contoso MFA-anspråk i token, vi se följande problem:

* Dag 1: En användare har MFA i Contoso och har åtkomst till App1 och inga ytterligare MFA uppmaning visas i Fabrikam.

* Dag 2: Användaren har åtkomst till appen 2 i Contoso, så nu vid åtkomst till Fabrikam, de måste det registrera för MFA.

Den här processen kan vara förvirrande och kan leda till att ta bort i inloggning slutföranden.

Dessutom även om Contoso har kapaciteten för MFA, är det inte alltid fallet Fabrikam skulle lita på Contoso MFA-principen.

Slutligen resource klient MFA fungerar för MSA: er och sociala ID och för partner organisationer som behöver inte konfigurera MFA.

Rekommendationen för MFA för B2B-användare är därför att alltid kräva MFA i inbjudande innehavaren. Detta krav kan leda till dubbla MFA i vissa fall, men när åtkomst till inbjudande innehavaren upplevelse för slutanvändare är förutsägbara: Lisa måste registrera för MFA med den inbjudande innehavaren.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Enhetsbaserad platsbaserad och riskbaserad villkorlig åtkomst för B2B-användare

När Contoso aktiverar principer för enhetsbaserad villkorlig åtkomst för företagsdata, förhindras åtkomst från enheter som inte hanteras av Contoso och inte är kompatibla med principer för Contoso-enheter.

Om B2B-användarens enhet inte hanteras av Contoso, blockeras åtkomst till B2B-användare från partnerorganisationer i oavsett vilken kontext som principerna tillämpas. Contoso kan dock skapa undantagslistor som innehåller specifika partneranvändare för att undanta dem från principen för enhetsbaserad villkorlig åtkomst.

#### <a name="location-based-conditional-access-for-b2b"></a>Platsbaserad villkorlig åtkomst för B2B

Principer för platsbaserad villkorlig åtkomst tillämpas för B2B-användare om organisationen som bjuder in kan skapa ett betrodda IP-adressintervall som definierar sina partnerorganisationer.

#### <a name="risk-based-conditional-access-for-b2b"></a>Riskbaserad villkorlig åtkomst för B2B

Riskbaserad inloggningsprinciper kan för närvarande inte användas för B2B-användare eftersom riskbedömningen utförs på hemorganisation för B2B-användaren.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar på Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Azure AD B2B-samarbetslicensiering](licensing-guidance.md)
* [Vanliga frågor och svar om Azure Active Directory B2B-samarbete](faq.md)
