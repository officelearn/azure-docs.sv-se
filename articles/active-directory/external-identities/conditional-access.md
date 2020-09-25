---
title: Villkorlig åtkomst för B2B-samarbets användare – Azure AD
description: Lär dig hur du tillämpar Multi-Factor Authentication-principer för Azure Active Directory B2B-användare.
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
ms.openlocfilehash: b47ec082daa6eee4ab593fdb5523087f9eb5b038
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91274154"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Villkorlig åtkomst för B2B-samarbets användare

## <a name="multi-factor-authentication-for-b2b-users"></a>Multi-Factor Authentication för B2B-användare
Med Azure AD B2B-samarbete kan organisationer tillämpa Multi-Factor Authentication-principer (MFA) för B2B-användare. Dessa principer kan tillämpas på klient-, app-eller individuell användar nivå, på samma sätt som de är aktiverade för heltids anställda och medlemmar i organisationen. MFA-principer tillämpas i resurs organisationen.

Exempel:
1. Admin-eller informations arbetare i företag A bjuder in användare från företag B till en program- *foo* i företag A.
2. Program- *foo* i företag A har kon figurer ATS för att kräva MFA vid åtkomst.
3. När användaren från företag B försöker komma åt appen *foo* i företagets A-klient uppmanas de att slutföra en MFA-utmaning.
4. Användaren kan konfigurera sina MFA med företag A och välja sina MFA-alternativ.
5. Det här scenariot fungerar för alla identiteter (Azure AD eller MSA, till exempel om användare i företag B autentiserar med sociala ID)
6. Företag A måste ha tillräckliga Premium Azure AD-licenser som stöder MFA. Användaren från företag B använder licensen från företag A.

Den bjudande innehavaren är alltid ansvarig för MFA för användare från partner organisationen, även om partner organisationen har MFA-funktioner.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Konfigurera MFA för användare i B2B-samarbete
Information om hur enkelt det är att konfigurera MFA för B2B-samarbets användare finns i hur i följande videoklipp:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B-användare MFA-upplevelse för erbjudande inlösen
Kolla in följande animering för att se hur du kan lösa in:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA-återställning för B2B-samarbets användare
För närvarande kan administratören kräva B2B-samarbets användare för att bara korrekturläsa igen med hjälp av följande PowerShell-cmdletar:

1. Anslut till Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Hämta alla användare med korrektur metoder

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Här är ett exempel:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Återställ MFA-metoden för en speciell användare för att kräva att B2B-samarbets användaren ställer in de olika metoderna igen. Exempel:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Varför utför vi MFA på resurs innehavaren?

I den aktuella versionen är MFA alltid i resurs innehavaren, på grund av förutsägbarhet. Anta till exempel att en Contoso-användare (Lisa) är inbjuden till Fabrikam och Fabrikam har aktiverat MFA för B2B-användare.

Om contoso har MFA-principen aktive rad för APP1 men inte APP2, så kan vi se följande problem om vi tittar på Contoso MFA-anspråk i token:

* Dag 1: en användare har MFA i Contoso och har åtkomst till app1. ingen ytterligare MFA-prompt visas i Fabrikam.

* Dag 2: användaren har åtkomst till app 2 i contoso, så nu vid åtkomst till Fabrikam måste de registreras för MFA där.

Den här processen kan vara förvirrande och kan leda till att slut för ande av inloggningar släpps.

Även om contoso har MFA-kapacitet är det inte alltid så att Fabrikam litar på Contoso MFA-principen.

Slutligen fungerar även resurs klient MFA för MSA: er och sociala ID: n och för partner organisationer som inte har MFA-inställningar.

Därför är rekommendationen för MFA för B2B-användare alltid att kräva MFA i den bjudande klienten. Detta krav kan leda till dubbel MFA i vissa fall, men när du ansluter till den bjudande klienten är slutanvändarens upplevelse förutsägbar: Lisa måste registreras för MFA med den bjudande klienten.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Enhets, platsbaserade och riskfyllda villkorliga åtkomst för B2B-användare

När contoso aktiverar enhets principer för villkorlig åtkomst för sina företags data, förhindras åtkomst från enheter som inte hanteras av Contoso och som inte är kompatibla med Contosos enhets principer.

Om B2B-användarens enhet inte hanteras av contoso, blockeras åtkomsten till B2B-användare från partner organisationerna i den kontext de här principerna tillämpas. Contoso kan dock skapa undantags listor som innehåller vissa partner användare som ska undanta dem från den enhets principen för villkorlig åtkomst.

#### <a name="mobile-application-management-policies-for-b2b"></a>Hanterings principer för mobil program för B2B

Det går inte att använda program skydds principer för villkorlig åtkomst för B2B-användare eftersom den bjudande organisationen inte har någon insyn i B2B-användarens hem organisation.

#### <a name="location-based-conditional-access-for-b2b"></a>Plats-baserad villkorlig åtkomst för B2B

Platsbaserade principer för villkorlig åtkomst kan tillämpas för B2B-användare om den bjudande organisationen kan skapa ett betrott IP-adressintervall som definierar sina partner organisationer.

#### <a name="risk-based-conditional-access-for-b2b"></a>Riskfylld villkorlig åtkomst för B2B

För närvarande går det inte att använda riskfyllda inloggnings principer för B2B-användare eftersom riskbedömningen utförs i B2B-användarens hem organisation.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Azure AD B2B-samarbetslicensiering](licensing-guidance.md)
* [Vanliga frågor och svar om Azure Active Directory B2B-samarbete](faq.md)
