---
title: Villkorlig åtkomst - Kräv MFA för administratörer - Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att kräva att administratörer utför multifaktorautentisering
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
ms.openlocfilehash: 90e8a8b0926575b5a40a8c0ca7820e31827434ec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755211"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Villkorlig åtkomst: Kräv MFA för administratörer

Konton som tilldelas administrativa rättigheter är måltavlor för angripare. Att kräva multifaktorautentisering (MFA) på dessa konton är ett enkelt sätt att minska risken för att dessa konton äventyras.

Microsoft rekommenderar att du behöver MFA på följande roller minst:

* Faktureringsadministratör
* Administratör för villkorlig åtkomst
* Exchange-administratör
* Global administratör
* Administratör för Helpdesk (lösenord)
* Lösenordsadministratör
* Säkerhetsadministratör
* SharePoint-administratör
* Användaradministratör

Organisationer kan välja att inkludera eller utesluta roller som de vill.

## <a name="user-exclusions"></a>Undantag från användare

Principer för villkorlig åtkomst är kraftfulla verktyg, vi rekommenderar att du utesluter följande konton från din policy:

* **Nödåtkomst** eller **break-glass-konton** för att förhindra kontoutelåsning för hela klienten. I det osannolika scenariot är alla administratörer utelåsta från din klientorganisation, ditt administrativa konto för nödåtkomst kan användas för att logga in på klienten vidta åtgärder för att återställa åtkomsten.
   * Mer information finns i [artikeln, Hantera konton för nödåtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Tjänstkonton** och **tjänsthuvudnamn**, till exempel Azure AD Connect Sync-konto. Tjänstkonton är icke-interaktiva konton som inte är kopplade till en viss användare. De används normalt av backend-tjänster som möjliggör programmatisk åtkomst till program, men används också för att logga in på system för administrativa ändamål. Tjänstkonton som dessa bör uteslutas eftersom MFA inte kan slutföras programmässigt. Samtal från tjänstens huvudnamn blockeras inte av villkorlig åtkomst.
   * Om din organisation har dessa konton som används i skript eller kod kan du överväga att ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan du utesluta dessa specifika konton från originalprincipen.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper till att skapa en princip för villkorlig åtkomst för att kräva att de tilldelade administrativa rollerna utför multifaktorautentisering.

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Katalogroller (förhandsgranskning)** och väljer minst följande roller:
      * Administratör för autentisering
      * Faktureringsadministratör
      * Administratör för villkorlig åtkomst
      * Exchange-administratör
      * Global administratör
      * Helpdesk-administratör
      * Lösenordsadministratör
      * Säkerhetsadministratör
      * SharePoint-administratör
      * Användaradministratör
   1. Under **Uteslut**väljer du **Användare och grupper** och väljer organisationens konton för nödåtkomst eller glasbrott. 
   1. Välj **Done** (Klar).
1. Under **Molnappar eller åtgärder** > **Inkludera**väljer du **Alla molnappar**och väljer **Klar**.
1. Under **Villkor** > **klientappar (förhandsversion)** ställer du in **Konfigurera** till **Ja**och väljer **Klar**.
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**, **Kräv multifaktorautentisering**och välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** för att skapa för att aktivera din princip.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)
