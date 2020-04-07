---
title: Villkorlig åtkomst - Kräv MFA för alla användare - Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att kräva att alla användare utför multifaktorautentisering
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3e7b1a656c92e37a709b57dae463f6644003e42
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755188"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Villkorlig åtkomst: Kräv MFA för alla användare

Som Alex Weinert, Directory of Identity Security på Microsoft, nämner i sitt blogginlägg [Din Pa $ $word spelar ingen roll:](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

> Ditt lösenord spelar ingen roll, men MFA gör! Baserat på våra studier är ditt konto mer än 99,9% mindre sannolikt att äventyras om du använder MFA.

Vägledningen i den här artikeln hjälper din organisation att skapa en balanserad MFA-princip för din miljö.

## <a name="user-exclusions"></a>Undantag från användare

Principer för villkorlig åtkomst är kraftfulla verktyg, vi rekommenderar att du utesluter följande konton från din policy:

* **Nödåtkomst** eller **break-glass-konton** för att förhindra kontoutelåsning för hela klienten. I det osannolika scenariot är alla administratörer utelåsta från din klientorganisation, ditt administrativa konto för nödåtkomst kan användas för att logga in på klienten vidta åtgärder för att återställa åtkomsten.
   * Mer information finns i [artikeln, Hantera konton för nödåtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Tjänstkonton** och **tjänsthuvudnamn**, till exempel Azure AD Connect Sync-konto. Tjänstkonton är icke-interaktiva konton som inte är kopplade till en viss användare. De används normalt av backend-tjänster som möjliggör programmatisk åtkomst till program, men används också för att logga in på system för administrativa ändamål. Tjänstkonton som dessa bör uteslutas eftersom MFA inte kan slutföras programmässigt. Samtal från tjänstens huvudnamn blockeras inte av villkorlig åtkomst.
   * Om din organisation har dessa konton som används i skript eller kod kan du överväga att ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan du utesluta dessa specifika konton från originalprincipen.

## <a name="application-exclusions"></a>Undantag från ansökan

Organisationer kan ha många molnprogram som används. Alla dessa program kan inte kräva lika säkerhet. Till exempel kan löne- och närvaroansökningar kräva MFA men cafeterian förmodligen inte. Administratörer kan välja att utesluta specifika program från sin princip.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper till att skapa en princip för villkorlig åtkomst för att kräva att de tilldelade administrativa rollerna utför multifaktorautentisering.

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Alla användare**
   1. Under **Uteslut**väljer du **Användare och grupper** och väljer organisationens konton för nödåtkomst eller glasbrott. 
   1. Välj **Done** (Klar).
1. Under **Molnappar eller åtgärder** > **Inkludera**väljer du **Alla molnappar**.
   1. Under **Uteslut**väljer du alla program som inte kräver multifaktorautentisering.
1. Under **Villkor** > **klientappar (förhandsversion)** ställer du in **Konfigurera** till **Ja**och väljer **Klar**.
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**, **Kräv multifaktorautentisering**och välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** för att skapa för att aktivera din princip.

### <a name="named-locations"></a>Namngivna platser

Organisationer kan välja att införliva kända nätverksplatser som kallas **namngivna platser** i sina principer för villkorlig åtkomst. Dessa namngivna platser kan innehålla betrodda IPv4-nätverk som de för en huvudkontorsplats. Mer information om hur du konfigurerar namngivna platser finns i artikeln [Vad är platsvillkoret i Azure Active Directory Villkorlig åtkomst?](location-condition.md)

I exempelprincipen ovan kan en organisation välja att inte kräva multifaktorautentisering om du öppnar en molnapp från företagets nätverk. I det här fallet kan de lägga till följande konfiguration i principen:

1. Under **Tilldelningar**väljer du**Villkorsplatser** **Conditions** > .
   1. Konfigurera **Ja**.
   1. Inkludera **valfri plats**.
   1. Uteslut **alla betrodda platser**.
   1. Välj **Done** (Klar).
1. Välj **Done** (Klar).
1. **Spara** dina principändringar.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)
